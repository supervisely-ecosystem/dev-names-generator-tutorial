# Development

Use default host and port:
127.0.0.1:80

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

How to run app from terminal using venv:
```sh
${PWD}/.venv/bin/python ${PWD}/.venv/bin/uvicorn src.main:app --host 127.0.0.1 --port 80
```