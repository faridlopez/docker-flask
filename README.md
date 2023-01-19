# Project 1 - Docker Python Flask Gunicor Backend API

In this repository we are going to create a basic backend (API services) with Docker Python Flask and Gunicorn.

Techs to use:
- Docker
- Python
- Flask
- Gunicorn

### Create Folder Structure for the applicacion

    .
    ├── ...
    ├── DockerProject                 # Project Folder
    │   ├── .env.dev                  # Env vars for Dev environment
    │   ├── backend_home              # Base folder for Python project
    │   │   ├── Dockerfile            # Dockerfile for Python project
    │   │   ├── app.py                # Base file for backend services
    │   │   ├── requirements.in       # Base req file
    │   │   ├── requirements.txt      # Req file
    │   │   ├── requirements_dev.txt  # Dev req file
    │   │   ├── venv                  # Python Virtual Environment
    │   │   └── wsgi.py               # wsgi file.
    └── └── docker-compose.yml        # Definition of docker containers


## Create new venv for Python 3.9: 
```
cd DockerProject/backend_home
python3.9 -m venv venv
```

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

## Run gunicorn: 
```
gunicorn -w 4 'app:app'
```
(If the file name is called app.py)

## Bind ip to all ips: 
```
gunicorn -w 4 -b 0.0.0.0:5050 'app:app'
```
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

## Create requirements.in based on requirements.txt
```
cp requirements.txt requirements.in
```

## Create requirements_dev.txt
```
pip-tools
```

## Install requirements_dev.txt:
```
pip install -r requirements_dev.txt
```

## Create requirements.txt based on requirements.in
```
pip-compile --output-file=requirements.txt requirements.in
```

## Clean req files
- Clean requirements.in
- Remove the packages that are rependencies of requirements.txt and another packages.
cause we only want the dependencies that are only required by requirements.txt file.

## Create requirements.txt and install packages
```
pip-compile --output-file=requirements.txt requirements.in
```

## Install packages
```
pip install -r requirements.txt
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
