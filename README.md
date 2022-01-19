# Development

For local development/debugging use `localhost:8000`.

Create venv:
```sh
python3 -m venv .venv
```

Install requirements:
```sh
. .venv/bin/activate
pip install -r requirements.txt
deactivate
```

How to run app from terminal using venv on local machine during debugging:
```sh
${PWD}/.venv/bin/python -m ${PWD}/.venv/bin/uvicorn src.main:app --host localhost --port 8000
```

# Deployment

Agent uses application configuration from `config.json`. 

`entrypoint` field - full command that agent will execute in container spawned from dockerimage defined in field `dockerimage`.

`port` - deployment port (we recommend to use 8000 by default like in fastapi)