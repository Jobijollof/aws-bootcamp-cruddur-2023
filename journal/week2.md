# Week 2 — Distributed Tracing

## Instructor:

Jessica Joy Kerr

### Distributed Tracing: 

This week was a bit technical for me. Ordinarily after class, or when i watch the instructional video i get a hang of what is being taught. However with a little bit of personal research i was able to figure out what the class was about. 

### What is Distributed Tracing?

Distributed tracing is a method of tracking application requests as they flow from frontend devices to backend services and databases. 
Developers can use distributed tracing to troubleshoot requests that exhibit high latency or errors.

It is also a method of monitoring and observing service requests in applications built on a microservices architecture(remember monolith architecture in week 0?). Distributed tracing is used by IT and DevOps teams to track requests or transactions through the application they are monitoring — gaining vital end-to-end observability into that journey. This lets them identify any issues, including bottlenecks and bugs, that could be having a negative impact on the application’s performance and affect user experience.


In other words, distributed tracing is a technique used to profile and monitor distributed systems. It involves collecting and correlating traces of requests as they traverse a distributed system, allowing engineers to understand the end-to-end latency, identify bottlenecks, and troubleshoot errors or performance issues.

In a distributed system, a single request may be processed by multiple microservices or components, each of which may be owned by a different team or organization. Distributed tracing allows engineers to trace the path of a request as it moves through these different services and components, collecting metadata such as timestamps, status codes, and other relevant information at each step.

Distributed tracing relies on instrumentation of each service or component in the system, which typically involves injecting unique identifiers into the request headers or payloads. These identifiers can then be used to correlate related requests and build a trace that shows the entire path of a request as it moves through the system.

Distributed tracing is a critical tool for troubleshooting and optimizing the performance of complex distributed systems, particularly in the context of modern microservices architectures.

### Is there a difference between Tracing and Distributed Tracing?

While tracing and distributed tracing are related concepts they however differ in scope and application.

Tracing refers to the process of monitoring and recording the execution of individual requests or transactions within a system. It typically involves instrumenting individual components or services to log events, timings, and other relevant data. Tracing is often used to troubleshoot issues within a ***SINGLE COMPONENT*** or service, such as identifying bottlenecks or errors.

Distributed tracing, on the other hand, is a technique used to profile and monitor distributed systems that involve ***MULTIPLE COMPONENTS*** or services. It involves collecting and correlating traces of requests as they traverse a distributed system, allowing engineers to understand the end-to-end latency, identify bottlenecks, and troubleshoot errors or performance issues. Distributed tracing typically requires instrumenting multiple components or services to capture data about the entire request path.

In summary, while tracing is focused on individual components or services, distributed tracing is focused on the interactions between those components or services as requests flow through the system. Distributed tracing provides a more holistic view of system performance and behavior, enabling engineers to identify issues that may be difficult or impossible to detect through traditional tracing techniques.

### Tools for Distributed Tracing:

There are a myraid of tools for distributed tracing but for the purpose of this bootcamp, we are limited to:

- Honeycomb

- AWS-XRay/Cloud Watch logs

- Roll Bar


## Honeycomb: 

[Instructional-Video](https://www.youtube.com/live/2GD9xCzRId4?feature=share)

Honeycomb is a full-stack cloud-based observability tool with support for events, logs, and traces. Honeycomb provides an easy-to-use distributed tracing solution.
Some of the key features of the Honeycomb distributed tracing tool includes:

- Quickly diagnose bottlenecks and optimize performance with a waterfall view to understand how your system is processing service requests

- Full-text search over trace spans and toggle to collapse and expand sections of trace waterfalls

- Provides Honeycomb beelines to automatically define key pieces of trace data like serviceName, name, timestamp, duration, traceID, etc.

### Implementing Honeycomb:

- Sign in/up [Honeycomb](https://ui.honeycomb.io/)

- Create a test environment

![create-enviroment](https://user-images.githubusercontent.com/113374279/222975439-74473c77-6ce1-451b-8256-5eca071fcea8.png)

- Choose a Name, pick a Colour and create the environment

![create-enviroment-purple](https://user-images.githubusercontent.com/113374279/222975529-9e68fd96-75c1-4e21-a16a-8253a7df1ec8.png)

- Copy the API key and go back to Gipod

Kindly note that Api Keys determine where data will land. Each enviroment has its own unique API key.


### Set Environment variables:

`gp env` is to make the enviroment variable persist when gitpod is restarted.

```
export HONEYCOMB_API_KEY="Your API Key from the created environment"
export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_API_KEY=""
gp env HONEYCOMB_SERVICE_NAME="Cruddur"
env | grep HONEY

```
![en-var](https://user-images.githubusercontent.com/113374279/222973463-b5406c55-7378-4789-bef4-c084fcb97467.png)

### What is the significance of Exporting Environment Variables:

If you export, any sub command you run which has its own sub-shells will get the set environment variable. If you do not export, the set enviroment  variable is set only for that shell.

- Set  honeycomb service name and add the OTEL service name, endpoint and headers to  `docker-compose.yml file`

```
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"

```
![backend-flask](https://user-images.githubusercontent.com/113374279/222976595-0514d819-b7c6-45a1-9069-c8f35176c5a6.png)

- Change dirctory to back-end-flask. `cd backend-flask`

On honeycomb  python instruction section, copy the installation commands there and add them to  `requirements.txt` file

![honeycomp-python](https://user-images.githubusercontent.com/113374279/222977919-79a4b47f-5f69-4247-a33c-5b9a14d41a9e.png)



```
opentelemetry-api
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests 

```
 
![install-package](https://user-images.githubusercontent.com/113374279/222978222-a205a4cb-7adf-4474-a174-585e28679024.png)

 - run `pip install -r requirements.txt`


-  copy the import statements from initialize section and paste it in  `app.py` file

![initialise-apppy](https://user-images.githubusercontent.com/113374279/222978097-a4c18b7b-e987-4935-b43d-3c48acc0e7a5.png)


```
# Honeycomb..................
from opentelemetry import trace  
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

```

- Still in `app.py` drop the following:

```
# Honey comb...................
# Initialize tracing and an exporter that can send data to Honeycomb
provider = TracerProvider()
processor = BatchSpanProcessor(OTLPSpanExporter())
provider.add_span_processor(processor)
trace.set_tracer_provider(provider)
tracer = trace.get_tracer(__name__)

```
The final code for `app.py` from honeycomb is:

```
# Initialize automatic instrumentation with Flask
app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()

```
🛑🛑🛑
`app = Flask(__name__)` already exists in `app.py`
copy everything except the above line.

![code-appy](https://user-images.githubusercontent.com/113374279/222978588-e9993a95-41e9-4e91-9470-0fba4923e76e.png)


- Add the following codes to `gitpod.yml` to automaticlly unlock ports.

```
ports:
  - name: frontend
    port: 3000
    onOpen: open-browser
    visibility: public
  - name: backend
    port: 4567
    visibility: public
  - name: xray-daemon
    port: 2000
    visibility: public

```

- Change directory to frontend `cd frontend-react-js`

- Run `npm i`

- Run `docker compose up`

![docker-composeup](https://user-images.githubusercontent.com/113374279/222978734-b349d5b3-be67-46bf-9f17-6cdb10451af1.png)

- Backend 

![backend-honey](https://user-images.githubusercontent.com/113374279/222978852-3625ac2d-9a4b-4a45-b17b-7f257a4ec0cb.png)

- Frontend

![front-end](https://user-images.githubusercontent.com/113374279/222978907-34df5758-c9c1-4114-97dd-baf3688018b5.png)


Debugging time:

I think the enviroment variable  `OTEL_SERVICE_NAME:` as Cruddur was not properly set. 
I got an error concerning the service name. (forgot to take pictures) so, honeycomb was not receiving any data.
Solution:
I unset the OTEL_SERVICE_NAME variable by running `unset OTEL_SERVICE_NAME` and exprted the variable name again.
I then did a `docker compose down`
After that i had to re-start the containers, i opened the links in the ports and i wasnt getting any data from the frontend. The backend was working perfectly. 
I checked the logs and I got an error, `react-script:not found`. I realised  that i was running `npm i` in the backend🤦‍♀️🤦‍♀️.
So, docker compose down again, changed directory to the frontend, started the containers and viola 💃💃💃💃

![dataset](https://user-images.githubusercontent.com/113374279/222979375-e03d466e-3c53-49f1-a52e-dc62afc1789d.png)


![datafrom-honey](https://user-images.githubusercontent.com/113374279/222979996-fcd40541-ce62-476d-a695-61bac6274f08.png)


![api-call](https://user-images.githubusercontent.com/113374279/222980041-d9a2fd63-8521-4a50-8214-acd598cd3038.png)

### SPAN Honeycomb:
A span is one event to Honeycomb. That event has fields, like parentID and traceID , which describe that span's relationship to other spans. A trace ties together a unit of work that occurs across multiple events (or spans) in a distributed service. A trace is a group of spans that all share the same traceID .

### Implementing Hardcoding a SPAN:

We are harcoding ``/api/activities/home`
At [honeycomb docs](https://docs.honeycomb.io/getting-data-in/opentelemetry/python/) check for the required command to aquire a tracer.

Copy the following into `home_activities.py`

```
from opentelemetry import trace` 
tracer = trace.get_tracer("home.activities")` 
with tracer.start_as_current_span("home-activities-mock-data"):

```
![hard-coding tracer](https://user-images.githubusercontent.com/113374279/222980380-31f4c20e-996c-40dc-a38e-b7ebe5def33a.png)



![data-tree](https://user-images.githubusercontent.com/113374279/222980614-7e432792-a3b4-4c4c-80f7-d49f15621c7f.png)


### Adding Attributes to the span

Back to honeycombs python docs, copy command for adding attributes,  add it to `home_activities.py` file.

```
span = trace.get_current_span()
span.set_attribute("user.id", user.id())

```

Edit this to this:

```
span = trace.get_current_span()
span.set_attribute("app.now", now.isoformat()) 
span.set_attribute("app.result_length", len(results))

```
![insofor](https://user-images.githubusercontent.com/113374279/224195366-78a00bc0-8975-4568-b28b-30d48bbc1be5.png)

![2nd](https://user-images.githubusercontent.com/113374279/224195906-6680fe6c-109d-48fe-a858-2854cf9b657b.png)

- Refresh honeycomb web pages and revisited my honeycome query page

![last-honey](https://user-images.githubusercontent.com/113374279/224196681-7f923292-0524-4c70-85ae-d8cd5ba39cac.png)

![data-honey](https://user-images.githubusercontent.com/113374279/224197083-e3cb79d7-8479-4c99-b1d1-87fda6243e71.png)



## AWS XRAY

- Updated `gitpod.yml` file, to automate `npm i`

- Go to  [AWS SDK](https://github.com/aws/aws-xray-sdk-python) for python instructions

- Change directory to `frontend-react-js`

- Run `npm i`

- Go to `backend-flask` open  `requirements.txt` and paste the following

`aws-xray-sdk`

- Change directory to `backend-flask` and run:

`pip install -r requirements.txt` to install dependencies.

In the backend go to `app.py` and paste the following:

```
# X-RAY ----------
from aws_xray_sdk.core import xray_recorder
from aws_xray_sdk.ext.flask.middleware import XRayMiddleware

```

```
# X-RAY ----------
xray_url = os.getenv("AWS_XRAY_URL")
xray_recorder.configure(service='backend-flask', dynamic_naming=xray_url)
XRayMiddleware(app, xray_recorder)

```
![apppy](https://user-images.githubusercontent.com/113374279/224198139-731c61f0-32ce-45a6-a890-f11d3e83ecc6.png)


### Setup AWS X-RAY Resources

- Create an `xray.json` file  in  `aws/json` folder.

- Add the commands below in the json file.

```

{
  "SamplingRule": {
      "RuleName": "Cruddur",
      "ResourceARN": "*",
      "Priority": 9000,
      "FixedRate": 0.1,
      "ReservoirSize": 5,
      "ServiceName": "backend-flask",
      "ServiceType": "*",
      "Host": "*",
      "HTTPMethod": "*",
      "URLPath": "*",
      "Version": 1
  }
}

```

- Create an Xray group


Run the following on the terminal

```
FLASK_ADDRESS="https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
aws xray create-group \
   --group-name "Cruddur" \
   --filter-expression "service(\"backend-flask\")"

```
![create-group](https://user-images.githubusercontent.com/113374279/224198740-5e06f4cf-1ecd-4e4d-a04f-038a021f353a.png)


![aws-create-group](https://user-images.githubusercontent.com/113374279/224199091-15e9cbc8-fcc6-40ba-9c31-9984f94c07c1.png)


- Create a sampling rule

```
aws xray create-sampling-rule --cli-input-json file://aws/json/xray.json

```

![after-group](https://user-images.githubusercontent.com/113374279/224199045-2162b3dd-c5db-494e-b4ee-4c2aa166e244.png)


![sampling-rule](https://user-images.githubusercontent.com/113374279/224199408-19bd2e4a-b5b1-43e3-ad94-38b975b82bde.png)



### AWS Xray Daemon

Paste this into the `docker-compose.yml` file

```
xray-daemon:
    image: "amazon/aws-xray-daemon"
    environment:
      AWS_ACCESS_KEY_ID: "${AWS_ACCESS_KEY_ID}"
      AWS_SECRET_ACCESS_KEY: "${AWS_SECRET_ACCESS_KEY}"
      AWS_REGION: "us-east-1"
    command:
      - "xray -o -b xray-daemon:2000"
    ports:
      - 2000:2000/udp

```

![x-ray daemon](https://user-images.githubusercontent.com/113374279/224205888-6a336b8a-b2c5-414f-a075-7b801e1cce1e.png)


- Add these two environment variables to the `backend-flask` section of  `docker-compose.yml` file.


```
AWS_XRAY_URL: "*4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}*"
AWS_XRAY_DAEMON_ADDRESS: "xray-daemon:2000"

```

- Run `docker compose up`


- Hit backend api to get data in AWS/Xray/query

![trace](https://user-images.githubusercontent.com/113374279/224200561-e5303e3f-deab-4c00-8455-34498c545ca0.png)


![trace2](https://user-images.githubusercontent.com/113374279/224200791-b1c87b53-afec-4dc1-8ff8-c62ff14b5cc0.png)


![segmnts](https://user-images.githubusercontent.com/113374279/224200949-55e8320f-10c1-41a5-8aa1-59dc3cab3425.png)


![segments2](https://user-images.githubusercontent.com/113374279/224200988-ec76c187-c060-4ad1-89b7-b1b2f89887c3.png)


### Rollbar

- Sign in/up to [Rollbar](rollbar.com)

- Choose the app you want in our case Flask (backend)

- In `backend-flask` open `requirement.txt`  and paste the following code:

`blinker`

`rollbar`


![blinker](https://user-images.githubusercontent.com/113374279/224201466-805afc80-7485-4b95-856f-bbf30fd85db4.png)


- Change directory to 'backend-flask' and Run:

`pip install -r requirements.txt`


- Copy access token  from rollbar and save it as an env var 


![rollbar-token](https://user-images.githubusercontent.com/113374279/224201839-fe110bc4-c947-47c9-a6ab-702bf2766208.png)


- Add the following commands from [rollbar](https://app.rollbar.com/a/jobinaibegbulem) to  `app.py` file.


```
import os
import rollbar
import rollbar.contrib.flask
from flask import got_request_exception

```

```
rollbar_access_token = os.getenv('ROLLBAR_ACCESS_TOKEN')
@app.before_first_request
def init_rollbar():
    """init rollbar module"""
    rollbar.init(
        # access token
        rollbar_access_token,
        # environment name
        'production',
        # server root directory, makes tracebacks prettier
        root=os.path.dirname(os.path.realpath(__file__)),
        # flask already sets up logging
        allow_logging_basic_config=False)

    # send exceptions from `app` to rollbar, using flask's signal system.
    got_request_exception.connect(rollbar.contrib.flask.report_exception, app)

```

- Add a test endpoint


```
@app.route('/rollbar/test')
def rollbar_test():
    rollbar.report_message('Hello World!', 'warning')
    return "Hello World!"

```

Set ROLLBAR_ACCESS_TOKEN, in docker-compose file.

`ROLLBAR_ACCESS_TOKEN: "${ROLLBAR_ACCESS_TOKEN}"`

![access-token](https://user-images.githubusercontent.com/113374279/224202913-73d0634e-4007-4944-ab7b-23a406163f0f.png)


`docker compose up`

- Hit the backend api

- Test Rollbar endpoint

![rollbar-logs](https://user-images.githubusercontent.com/113374279/224203448-d9e4b82a-89bf-4561-8728-42ffbd5466f7.png)


![hello world](https://user-images.githubusercontent.com/113374279/224203052-e359ccf2-7f62-40a4-804d-0e3021c193e8.png)

- Go back to rollbar

- Click on flask/view item

![view item](https://user-images.githubusercontent.com/113374279/224203194-a24d1c27-4255-48f8-b7d8-9fa51dd5ea64.png)

![end-rollbar](https://user-images.githubusercontent.com/113374279/224204212-a1c805ff-2b88-4931-bc5c-b662dbaaf8c0.png)

- Delibrately deleted some part of the code to create an error. To see if errors will be logged as well.


![error-rollbar](https://user-images.githubusercontent.com/113374279/224204497-6571e29e-716a-4f43-b0f0-7e17e6b62fe5.png)


![error2-rollbar](https://user-images.githubusercontent.com/113374279/224204556-4b5d9a17-f87e-4a31-aa45-db1a7ef74972.png)









