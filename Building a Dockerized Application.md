# Building a Dockerized Application
First lets create a simple Flask Web App (Flask is a lightweight WSGI web application framework. It is designed to make getting started quick and easy, with the ability to scale up to complex applications.)

## Simple Flask Web App:
Set up Our Environment: Obv Install Docker and also Ensure we have python installed. 

### Creating a Flask App:
1) Create a file named : `app.py`  

2) Copy paste the below code to that `app.py`

 ```sh 
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, Our own Dockerized World!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

```

3) Create a `requirements.txt` file and copy paste the below. (Note: These are old version and have a lot of vulnerabilities- only for testing)
```sh
Flask==2.0.3
Werkzeug==2.0.3

``` 
4) ### Write a DockerFile:
- Make a file named `DockerFile` (without any extension) and paste the below code.
```sh
# Use an official Python runtime as a parent image
FROM python:3.9-slim

# Set the working directory
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 5000 available to the world outside this container
EXPOSE 5000

# Define environment variable
ENV FLASK_ENV=development

# Run app.py when the container launches
CMD ["python", "app.py"]

```
5) Build and Run the Docker Image:
- Build `docker build -t flask-app .`
- Run `docker run -p 5000:5000 flask-app`

6) ### Test: 
- Open a browser and go to `http://localhost:5000`
