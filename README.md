# Project 1 - Docker Python Flask Gunicor Backend API

In this repository we are going to create a basic backend (API services) with Docker Python Flask and Gunicorn.

Techs to use:
- Docker
- Python
- Flask
- Gunicorn

To tun this project you should have installed Docker and Python (3.9)

### Create Folder Structure for the applicacion

    .
    ├── ...
    ├── DockerProject                 # Project Folder
    │   ├── .env.dev                  # Env vars for Dev environment
    │   ├── backend_home              # Base folder for Python project
    │   │   ├── Dockerfile            # Dockerfile for Python project
    │   │   ├── app.py                # Base file for backend services
    │   │   ├── requirements.txt      # Req file
    │   │   ├── venv                  # Python Virtual Environment
    │   │   └── wsgi.py               # wsgi file.
    └── └── docker-compose.yml        # Definition of docker containers


## Create new venv for Python 3.9: 
```
cd DockerProject/backend_home
python3.9 -m venv venv
```
Note: if this commands fails try
```
python3.9 -m venv /path/to/directory
```
Replace /path/to/directory by the actual directory where you are going to install the virtual environment.

## Activate virtual Environment: 
```
source ./venv/bin/activate
```

## Install flask: 
```
pip install flask
```

## Create app.py file:
```
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"
```

## Start flask server: 
```
flask run
```
(If the file has a hello.py name, the command should be flask --app hello run)

## Install Gunicorn: 
```
pip install gunicorn
```

## Run gunicorn, binding ip to all ips: 
```
gunicorn -w 4 -b 0.0.0.0:5050 'app:app'
```
(If the file name is called app.py)

After this you can check in your web browser:
http://127.0.0.1:5050/
And you should see the Hello World

## Create wsgi.py file:
```
from app import app

if __name__ == "__main__":
    app.run()
```

## Run wsgi.py with gunicorn:
```
gunicorn -w 4 -b 0.0.0.0:5050 -m 007 'wsgi:app'
```

## Create requirements.txt file:
```
pip freeze > requirements.txt
```

## Create Dockerfile
```
FROM python:3.9

RUN apt update

RUN apt -y upgrade

# set work directory
WORKDIR /app

# set environment variables
# To not write pyc files.
ENV PYTHONDONTWRITEBYTECODE 1
# To not buffers stdout and stderr
ENV PYTHONUNBUFFERED 1

# install dependencies
RUN pip install --upgrade pip
COPY requirements.txt /app/requirements.txt
RUN pip install -r requirements.txt

# copy project
COPY . /app/
```

## Create the DockerProject/.env.dev file
In the DockerProject create the file .env.dev with the following content:
```
FLASK_DEBUG=1
```

## Create docker-compose file
```
version: '3.8'
services:
  backend:
    container_name: back_container
    image: back_image
    command: gunicorn -w 4 -b 0.0.0.0:5000 -m 007 'wsgi:app'
    env_file: ./.env.dev
    tty: true
    build:
      context: backend_home
    volumes:
      - "./backend_home:/app"
    ports:
      - 5050:5000
    networks:
      - vnet
networks:
  vnet:
```

## Execute docker-compose
```
docker-compose up -d
```
After this you can check in your web browser again: http://127.0.0.1:5050/ And you should see the Hello World running from the Docker Container.
