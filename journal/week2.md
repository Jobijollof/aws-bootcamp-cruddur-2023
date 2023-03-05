# Week 2 — Distributed Tracing

## Instructor:

Jessica Joy Kerr

### Distributed Tracing: [Class](https://www.youtube.com/live/2GD9xCzRId4?feature=share)

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


### Honeycomb:

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
(`gp env` is to make the enviroment variable persist when gitpod is restarted)

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

On honeycombs  python instruction section, copied the installation commands there and added them to my requirements.txt file
- Add the following files to our `requirements.txt`

```
opentelemetry-api
opentelemetry-sdk 
opentelemetry-exporter-otlp-proto-http 
opentelemetry-instrumentation-flask 
opentelemetry-instrumentation-requests 

```

`cd backend-flask` 

- run `pip install -r requirements.txt`

-  copy the import statements from initialize section and paste it in  `app.py` file

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



`cd frontend-react-js`

`npm i`

`docker compose up`

![docker-composeup]

- Added the following codes to `gitpod.yml` to keep ports open to avaid manually opening it.

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

Debugging time:

The first mistake was Iset OTEL_SERVICE_NAME: as Cruddur. 
I got an error concerning the service name. (forgot to take pictures) 
Solution:
I unset the OTEL_SERVICE_NAME variable by running `unset OTEL_SERVICE_NAME`
The second mistake, was tIat i was running `npm i` in the backend.  The frontend was no longer receiving data.
I checked the logs and I got an error, `react-script:not found`. I realised my mistake and did the right thing by running `the npm i` in the frontend and everything worked and Honeycomb had started receiving my Data.

### Hardcoding a SPAN

We are harcoding ``/api/activities/home`
At {honeycomb docs](https://docs.honeycomb.io/getting-data-in/opentelemetry/python/) check for the required command to aquire a tracer.

Copy the following into `home_activities.py`

```
from opentelemetry import trace` 
tracer = trace.get_tracer("home.activities")` 
with tracer.start_as_current_span("home-activities-mock-data"):

```

- Adding Attributes to the span

Back to honeycombs python docs, copy command for adding attributes,  added it to `home_activities.py` file.

```
span = trace.get_current_span()
span.set_attribute("user.id", user.id())

```

Edit this to this:

```
span.set_attribute("app.now", now.isoformat()) 
span.set_attribute("app.result_length", len(results))

```

