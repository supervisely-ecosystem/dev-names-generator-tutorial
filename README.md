# Names Generator Tutorial

## Introduction

This tutorial aims to explain how to create a simple Supervisely app using new app engine `2.0.0` based on `FastAPI`, which is much more flexible comparing to it's previous counterpart.

The app functionality is not just randomly generated names.

Agent uses application configuration from `config.json`.

`entrypoint` -  full command that agent will execute in container spawned from docker image defined in field `docker_image`.
`port` - deployment port (we recommend to use 8000 by default like in FastAPI)

## Setting up a Working Environment

**Step 1.** Prepare `~/supervisely.env` file with credentials. [Learn more here.](https://developer.supervise.ly/getting-started/basics-of-authentication#how-to-use-in-python)

**Step 2.** Clone [repository](https://github.com/supervisely-ecosystem/dev-names-generator-tutorial) with source code and demo data and create [Virtual Environment](https://docs.python.org/3/library/venv.html).

```bash
git clone https://github.com/supervisely-ecosystem/dev-names-generator-tutorial
cd dev-names-generator-tutorial
./create_venv.sh
```

**Step 3.** Open repository directory in Visual Studio Code.

```bash
code -r .
```

**Step 4.** Open `debug.env` and specify debug directory.

```python
SLY_APP_DATA_DIR="src/debug_data"
```

**Step 5.** Start debugging `src/main.py`

## How to start

### Import libraries

```python
import asyncio
import json
import os
import sys
import time

import names
import supervisely as sly
from dotenv import load_dotenv
from fastapi import Depends, FastAPI, Request
```

### Set up app root directory

```python
app_dir = os.getcwd()  # app root directory (working directory)
sys.path.append(app_dir)
print(f"App root directory: {app_dir}")
```

Output:

```
home/admin/projects/dev-names-generator
```

### Load .env variables from file for debug

```python
load_dotenv(os.path.join(app_dir, "debug.env"))
```

### Initialize state and data variables default values

```python
sly.app.StateJson({"name": "abc", "counter": 0})
sly.app.DataJson({"max": 123, "counter": 0})
```

### Initialize app object and mount html templates folder
​
```python
app = FastAPI()
sly_app = sly.app.fastapi.create()
app.mount("/sly", sly_app)
templates = sly.app.fastapi.Jinja2Templates(directory="templates")
```

### Render html template

```python
@app.get("/")
async def read_index(request: Request):
    return templates.TemplateResponse("index.html", {"request": request})
```

## App functionality

### Generate buttons

In this example we generate names in 3 ways

1. Post response
2. WebSocket
3. Sync

#### Post response

<img src="">

```html
<el-button type="primary" @click="post('/generate')">Get name (post response)</el-button>
```

```python
@app.post("/generate")
async def generate(
    request: Request, state: sly.app.StateJson = Depends(sly.app.StateJson.from_request)
):
    state["name"] = names.get_first_name()
    await state.synchronize_changes()
    # return state.get_changes()  # hidden functionality

    # for debug to test app data directory real-time synchroniation with team files
    file_path = os.path.join(sly.app.get_data_dir(), "data.json")
    with open(file_path, "w") as my_demo_file:
        json.dump(state, my_demo_file, indent=4)
```

#### WebSocket

<img src="">

```html
<el-button type="primary" @click="post('/generate-ws')">Get name (websocket)</el-button>
```

```python
@app.post("/generate-ws")
async def generate_ws(
    request: Request, state: sly.app.StateJson = Depends(sly.app.StateJson.from_request)
):
    state["name"] = names.get_first_name()
    await state.synchronize_changes()  
```

#### Sync

<img src="">

```html
<el-button type="primary" @click="post('/sync-generate')">Get name (sync)</el-button>
```

```python
@app.post("/sync-generate")
def sync_generate(
    request: Request, state: sly.app.StateJson = Depends(sly.app.StateJson.from_request)
):
    state["name"] = names.get_first_name()
    time.sleep(50)
    sly.app.fastapi.run_sync(state.synchronize_changes())
```

### Shutdown buttons

#### Direct shutdown

This button directly shutdown the app

<img src="">

```html
<div style="margin-top: 10px;">
                <el-button type="danger" @click="post('/sly/shutdown')">Shutdown app (sly endpoint)</el-button>
</div>
```

#### Do and shutdown

This button will execute your code and then shutdown the app

<img src="">

```html
<div style="margin-top: 10px;">
    <el-button type="danger" @click="post('/do-then-shutdown')">Do custom things and then shutdown programmatically</el-button>
</div>
```

```python
@app.post("/do-then-shutdown")
async def do_then_shutdown(
    request: Request, state: sly.app.StateJson = Depends(sly.app.StateJson.from_request)
):
    print("do something here and then manual shutdown")
    sly.app.fastapi.shutdown()
```


### Counter buttons

#### Count state variables

```html
<div style="margin-top: 10px;">
                <div>State counter: {{state.counter}}</div>
                <el-button type="primary" @click="post('/count-state')">Count state var from 0 to 10</el-button>
</div>
```

```python
@app.post("/count-state")
async def count_state(
    request: Request, state: sly.app.StateJson = Depends(sly.app.StateJson.from_request)
):
    for i in range(10):
        await asyncio.sleep(0.5)
        state["counter"] = i
        await state.synchronize_changes()
```

#### Count data variables

```html
<div style="margin-top: 10px;">
    <div>Data counter: {{data.counter}}</div>
    <el-button type="primary" @click="post('/count-data')">Count data var from 0 to 10</el-button>
</div>
```

```python
@app.post("/count-data")
async def count_state(
    request: Request, state: sly.app.StateJson = Depends(sly.app.StateJson.from_request)
):
    data = sly.app.DataJson()
    for i in range(10):
        await asyncio.sleep(0.5)
        data["counter"] = i
        await data.synchronize_changes()
```
