# Week 2 — Distributed Tracing

Api Keys determine where data will land. Each enviroment has its own unique API key.

gZseQvXNV1eijUiZXdjj5I Api key

- Set Environment variables:(gp env is to make the enviroment variable persist when gitpod is restarted)

```
export HONEYCOMB_API_KEY="Your API Key from the created environment"
export HONEYCOMB_SERVICE_NAME="Cruddur"
gp env HONEYCOMB_API_KEY=""
gp env HONEYCOMB_SERVICE_NAME="Cruddur"

```


