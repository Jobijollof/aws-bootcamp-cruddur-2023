# Week 1 — App Containerization

Guest Instructors:

James Spurin  - Docker Captain

Edith Puclla  -  Docker Captain


[Spending-considerations](https://youtu.be/OAMHu1NiYoI)

Check  my AWS account to make sure I am still on the free tier and have not incurred 
any cost.

Go to my avatar on Gitpod to check billing to confirm how much credit I have.


[Container-Security-Considerations](https://www.youtube.com/watch?v=OjZz4D0B-cA&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=25)	


### What is docker?

Docker is a software platform that allows you to build, test, and deploy applications quickly. Docker packages software into standardized units called containers that have everything the software needs to run including libraries, system tools, code, and runtime.

### What is a container?

A container is a unit of software that packages code and its dependencies so the application runs quickly and reliably across computing environments.

### Why Containerization?

Containerization is a type of virtualization in which all the components of an application are bundled into a single container image and can be run in isolated user space on the same shared operating system. Containers are lightweight, portable, and highly conducive to automation.

### Wny Docker?
Fast deployment, ease of creating new instances, and faster migrations. Ease of moving and maintaining your applications. Better security, less access needed to work with the code running inside containers, and fewer software dependencies.


### What is Docker hub?:

Docker Hub is a Docker Registry, a cloud-hosted version, open-source, scalable server-side application, and stateless. It can manage the sharing and storage of Docker images. Using Docker, developers can access it as public and create their own private repositories space and automate application build custom functions, work-groups, and webhooks.

Alternatives to Docker:

1. Amazon Elastic Container Registry (ECR)

2. JFrog Artifactory.

3. Azure Container Registry.

4. Red Hat Quay.

5. Google Container Registry.

6. Harbor.

7. Nexus Repository Manager.

8. Sandboxie.

## Add Dockerfile

### Create a file here: `backend-flask/Dockerfile`

- Paste this code into the file

```

FROM python:3.10-slim-buster

WORKDIR /backend-flask

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt

COPY . .

ENV FLASK_ENV=development

EXPOSE ${PORT}
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]

```

```
cd backend-flask
pip3 install -r requirements.txt
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..

```
Paste code one after the other on the terminal.

- Unlock the port tab on the terminal (you would see a tiny padlock,click on it to open it)

- Open the link associated with  port 4567 

- Add the following to the url to `/api/activities/home`

- Output is a  of Json code


### Unset Environment variables

`unset FRONTEND_URL`

`unset BACKEND_URL`

`env |grep` (To check if it was unset. 
alternatively)

`env | grep _URL` (env vars should be absent)

### Build Container

The docker build command builds Docker images from a Dockerfile and a “context”. A build's context is the set of files located in the specified PATH or URL . The build process can refer to any of the files in the context. For example, your build /api/activities/homecan use a COPY instruction to reference a file in the context.

```
docker build -t  backend-flask ./backend-flask
docker build -t  backend-flask:latest ./backend-flask

```

### Run in background

`docker container run --rm -p 4567:4567 -d backend-flask`

A little story time. At this point i had started to feel like a docker Captain. Everything was working as expected without any errors. instead of running `docker container run --rm -p 4567:4567 -d backend-flask`, i ran `docker run --rm -p 4567:4567 -d backend-flask` because in my head why will the command include `container` 🤣 the errors came upon me like an earthquake.  I decided to do the right thing by copying and pasting the right command.  error: Bind for 0.0.0.0 failed:port is already allocated.😔 All i could have done was follow instructions. Anyways, i googled the error on stack overflow, and i saw that i had to do the following:

`docker container ls` This should list the container ID.

`docker stop <container ID>`

Advice from stack overflow worked 💃😮‍💨. The next step was to set environment variables.


### Set Environment Variables

`set FRONTEND_URL="*"`

`set BACKEND_URL="*"`

`docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask`


## Containerize Frontend

### Run NPM Install

We have to run NPM Install before building the container since it needs to copy the contents of node_modules

`cd frontend-react-js`

`npm i`

## Create Docker File

### Create a file here: `frontend-react-js/Dockerfile`

```
FROM node:16.18

ENV PORT=3000

COPY . /frontend-react-js
WORKDIR /frontend-react-js
RUN npm install
EXPOSE ${PORT}
CMD ["npm", "start"]

```

`cd .. `

## Build Container

`docker build -t frontend-react-js ./frontend-react-js`

## Run Container

`docker run -p 3000:3000 -d frontend-react-js`

## Multiple Containers

### Create a docker-compose file

### Create `docker-compose.yml` at the root of your project.

```
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur

```

-  click on `docker-compose.yml` and click `compose up`

### Observation:

I had followed all the instructions to build frontend and backend, i ran `docker compose up` but i got an error that `Bind for 0.0.0.0 failed:port is already allocated` i used the container id to kill the process. This problem persisted all night till i realised that i could just run `compose up` and the two containers will build at the same time. I did not have to build the containers seprately and subsequently `compose up`. 

![docker](https://github.com/Jobijollof/aws-bootcamp-cruddur-2023-/blob/main/journal/images/docker-composeup.png)



## Api/Create a Notification Feature, Backend/Frontend:

[Notification-feature](https://www.youtube.com/watch?v=OjZz4D0B-cA&list=PLBfufR7vyJJ7k25byhRXJldB5AiwgNnWv&index=25)

Open `openapi.yml file` and add the following.


```
  /api/activities/notifications:
    get:
      description: 'Return a feed of activity for all the people i follow'
      tags:
        - activities
        
      parameters: []
      responses:
        '200':
          description: Returns an array of activities
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Activity'
                
```          
          
                  
Open the `backend-flask/services` create a `notifications_activities` file and add the following:

```

from datetime import datetime, timedelta, timezone
class NotificationsActivities:
  def run():
    now = datetime.now(timezone.utc).astimezone()
    results = [{
      'uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
      'handle':  ' Jobi Jollof',
      'message': 'Elections in Nigeria',
      'created_at': (now - timedelta(days=2)).isoformat(),
      'expires_at': (now + timedelta(days=5)).isoformat(),
      'likes_count': 5,
      'replies_count': 1,
      'reposts_count': 0,
      'replies': [{
        'uuid': '26e12864-1c26-5c3a-9658-97a10f8fea67',
        'reply_to_activity_uuid': '68f126b0-1ceb-4a33-88be-d90fa7109eee',
        'handle':  'Worf',
        'message': 'This post has no honor!',
        'likes_count': 0,
        'replies_count': 0,
        'reposts_count': 0,
        'created_at': (now - timedelta(days=2)).isoformat()
      }],
    },
   
    ]
    return results

```

Open frontend-react-js/src/pages add a new file NotificationsFeedPage.js and add the following:

```

import './NotificationsFeedPage.css';
import React from "react";

import DesktopNavigation  from '../components/DesktopNavigation';
import DesktopSidebar     from '../components/DesktopSidebar';
import ActivityFeed from '../components/ActivityFeed';
import ActivityForm from '../components/ActivityForm';
import ReplyForm from '../components/ReplyForm';

// [TODO] Authenication
import Cookies from 'js-cookie'

export default function NotificationsFeedPage() {
  const [activities, setActivities] = React.useState([]);
  const [popped, setPopped] = React.useState(false);
  const [poppedReply, setPoppedReply] = React.useState(false);
  const [replyActivity, setReplyActivity] = React.useState({});
  const [user, setUser] = React.useState(null);
  const dataFetchedRef = React.useRef(false);

  const loadData = async () => {
    try {
      const backend_url = `${process.env.REACT_APP_BACKEND_URL}/api/activities/notifications`
      const res = await fetch(backend_url, {
        method: "GET"
      });
      let resJson = await res.json();
      if (res.status === 200) {
        setActivities(resJson)
      } else {
        console.log(res)
      }
    } catch (err) {
      console.log(err);
    }
  };

  const checkAuth = async () => {
    console.log('checkAuth')
    // [TODO] Authenication
    if (Cookies.get('user.logged_in')) {
      setUser({
        display_name: Cookies.get('user.name'),
        handle: Cookies.get('user.username')
      })
    }
  };

  React.useEffect(()=>{
    //prevents double call
    if (dataFetchedRef.current) return;
    dataFetchedRef.current = true;

    loadData();
    checkAuth();
  }, [])

  return (
    <article>
      <DesktopNavigation user={user} active={'notifications'} setPopped={setPopped} />
      <div className='content'>
        <ActivityForm  
          popped={popped}
          setPopped={setPopped} 
          setActivities={setActivities} 
        />
        <ReplyForm 
          activity={replyActivity} 
          popped={poppedReply} 
          setPopped={setPoppedReply} 
          setActivities={setActivities} 
          activities={activities} 
        />
        <ActivityFeed 
          title="Notifications" 
          setReplyActivity={setReplyActivity} 
          setPopped={setPoppedReply} 
          activities={activities} 
        />
      </div>
      <DesktopSidebar user={user} />
    </article>
  );
}


```

In the `frontend-react-js/src/app.js` file make these two additions:

```
import NotificationsFeedPage from './pages/NotificationsFeedPage';

```

```
{
  path: "/notifications",
  element: <NotificationsFeedPage />
},

```
![addition](https://user-images.githubusercontent.com/113374279/222031951-18088dc5-8ed0-4b31-b0ee-dc464ae179eb.png)




































































































