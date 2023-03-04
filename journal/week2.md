# Week 2 — Distributed Tracing

Instructor:

Jessica Joy Kerr

Api Keys determine where data will land. Each enviroment has its own unique API key.


- Set Environment variables:(gp env is to make the enviroment variable persist when gitpod is restarted)

```
export HONEYCOMB_API_KEY="Your API Key from the created environment"
export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_API_KEY=""
gp env HONEYCOMB_SERVICE_NAME="Cruddur"
env | grep HONEY

```

Exporting Environment Variables: if you export, any sub command you run which has its own  the sub-shells will get the set environment variable. If you do not export, the set enviroment  variable is set only for that shell.

Add the following Env Vars to `backend-flask` in docker compose

```
OTEL_EXPORTER_OTLP_ENDPOINT: "https://api.honeycomb.io"
OTEL_EXPORTER_OTLP_HEADERS: "x-honeycomb-team=${HONEYCOMB_API_KEY}"
OTEL_SERVICE_NAME: "${HONEYCOMB_SERVICE_NAME}"

```
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

