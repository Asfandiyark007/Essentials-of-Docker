# Custom Docker Networks
Custom docker Networks allow containers to communicate with each other. We will build our own mini project where:
1) One container runs a backend (Python Flask) that generates random jokes.
2) Another container runs a frontend (static HTML + JavaScript) that fetches and displays those jokes.

# Step 1: Project Setup:
## Create the project Directory:
- We will Structure our files like below.
```sh
docker-network-example/
│
├── backend/
│   ├── app.py
│   ├── requirements.txt
│   ├── Dockerfile
│
├── frontend/
│   ├── index.html
│   ├── Dockerfile
│
├── docker-compose.yml

```
# Step 2: Write the Backend (Joke API)
Create File: `backend/app.py` and paste below code (It will serve random jokes as JSON response).
```sh
from flask import Flask, jsonify
from flask_cors import CORS
import random

app = Flask(__name__)
CORS(app)  # Enable CORS for all routes

jokes = [
    "Why don’t scientists trust atoms? Because they make up everything!",
    "Why did the scarecrow win an award? He was outstanding in his field!",
    "Why don’t skeletons fight each other? They don’t have the guts."
]

@app.route('/joke', methods=['GET'])
def get_joke():
    return jsonify({"joke": random.choice(jokes)})

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5001)


```
### Create File: `backend/requirements.txt` and paste below code:
```sh
Flask==2.0.3
Flask-Cors==3.0.10
Werkzeug==2.0.3
```
### Create the `Dockerfile` for the backend:
```sh
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py app.py

EXPOSE 5001

CMD ["python", "app.py"]

```
# Step 3: Now lets Write the Frontend:

1) Create File: `frontend/index.html`
- This simple HTML page fetches jokes from the backend and displays them.

```ch
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Joke Generator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
        }
    </style>
</head>
<body>
    <h1>Random Joke Generator</h1>
    <p id="joke">Click the button to get a joke!</p>
    <button onclick="fetchJoke()">Get Joke</button>

    <script>
    async function fetchJoke() {
        try {
            const response = await fetch("http://localhost:5001/joke");
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            const data = await response.json();
            console.log("Joke received:", data); // Debugging log
            document.getElementById('joke').innerText = data.joke;
        } catch (error) {
            console.error("Error fetching joke:", error);
            document.getElementById('joke').innerText = "Failed to fetch a joke. Please try again!";
        }
    }
</script>

</body>
</html>

```
2) Create File: `frontend/Dockerfile`
- Create the Dockerfile for the frontend:
```ch
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80

```
# Step 4: Define the Custom Network:

1) Create File: `docker-compose.yml` to define the service and the network:
```ch
version: "3.8"

services:
  backend:
    build: ./backend
    networks:
      - joke-network
    ports:
      - "5001:5001"

  frontend:
    build: ./frontend
    networks:
      - joke-network
    ports:
      - "8080:80"

networks:
  joke-network:
```
# Step 5: Build and Run the Project:

1) Build the Docker Image:
```ch
docker-compose build
```
2) Start the Services:

```ch
docker-compose up
```
3) Access the Frontend:
```ch
http://localhost:8080
```

# Enjoy the Jokes (We learned Custom Docker networks using Docker-compose `Simplifies multi-container management` ) 
