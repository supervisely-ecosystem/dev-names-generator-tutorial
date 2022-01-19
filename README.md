# Development

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
${PWD}/.venv/bin/python ${PWD}/.venv/bin/uvicorn src.main:app --host 0.0.0.0 --port 8080
```