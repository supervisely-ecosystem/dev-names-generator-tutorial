Create venv:
```sh
python3 -m venv venv
```

install requirements:
```sh
. venv/bin/activate
pip install -r requirements.txt
deactivate
```

how to run app from terminal:
```sh
. venv/bin/activate
${PWD}/venv/bin/python ${PWD}/venv/bin/uvicorn src.main:app --host 0.0.0.0 --port 80
```