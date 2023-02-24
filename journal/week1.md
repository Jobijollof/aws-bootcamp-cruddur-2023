# Week 1 — App Containerization

Guest Instructors:

James Spurin  - Docker Captain

Edith Puclla  -  Docker Captain

Check  my AWS account to make sure I am still on the free tier and have not incurred 
any cost.

Go to my avatar on Gitpod to check billing to confirm how much credit I have.


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

- I unlocked the port tab on my terminal (you would see a tiny padlock,click on it to open it)

- I opened the link associated with 4567 in my browser

- added the following to the url to `/api/activities/home`

- I got a series of code


### Unset Environment variables

`unset FRONTEND_URL`

`unset BACKEND_URL`

`env |grep` (To check if it was unset. 
alternatively)

`env | grep _URL` (env vars should be absent)

### Build Container

The docker build command builds Docker images from a Dockerfile and a “context”. A build's context is the set of files located in the specified PATH or URL . The build process can refer to any of the files in the context. For example, your build can use a COPY instruction to reference a file in the context.

```
docker build -t  backend-flask ./backend-flask
docker build -t  backend-flask:latest ./backend-flask

```

### Run in background

`docker container run --rm -p 4567:4567 -d backend-flask`

Here, i was pretty confused. I ran the above command and asumed i had an error copying the command cause in my head why will container be in the ommand. SO, i ran the command again, this time i removed the container: `docker run --rm -p 4567:4567 -d backend-flask`  erorr: Bind for 0.0.0.0 faile:port is already allocated. 
I googled the error and on stack overflow, i saw that i had to do the following:

`docker container ls` This should list the container ID.

`docker stop <container ID>`

I noticed my terminal output was different for Andrews in the tutorial. SO i clicked on Logs on docker and I got my logs. So everything works perfectly now.

### Set Environment Variables

`set FRONTEND_URL="*"`

`set BACKEND_URL="*"`

`docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask`


## Containerize Frontend

###Run NPM Install

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

- Create `docker-compose.yml` at the root of your project.

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



































































































