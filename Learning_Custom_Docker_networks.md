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
import random

app = Flask(__name__)

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
```
### Create the DockerFile for the backend:
```sh
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py app.py

EXPOSE 5001

CMD ["python", "app.py"]

```