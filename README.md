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

src.main:app --reload --host 0.0.0.0 --port 8080
src.main:app --reload --host 0.0.0.0 --port 8080 --log-config log_conf.yml


https://stackoverflow.com/questions/60205056/debug-fastapi-application-in-vscode