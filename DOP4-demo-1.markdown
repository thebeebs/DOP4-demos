# Snippets for DOP4

Make Directory and move into it

```
mkdir demo-github-actions && cd demo-github-actions
```

Init Git

```
git init
```

Create a file app.py

```
touch app.py
```

Open VS code

```
code .
```

Create a base flask file. Flask is a micro web framework written in Python. And I am going to write a very basic web app. I add the basic flask code to this app.py file. 

```
"""Main application file"""
from flask import Flask
app = Flask(__name__)
```

Create a flask route which will reverese any string that is provided.

```
@app.route('/<random_string>')
def returnBackwardsString(random_string):
    """Reverse and return the provided URI"""
    return "".join(reversed(random_string))
```

Add some code to run the app and listen on port 8080

```
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
```

Create the app_test.py file


app_test.py: a unit test file for app.py to ensure that the string is reversed correctly

```
"""Unit test file for app.py"""
from app import returnBackwardsString
import unittest

class TestApp(unittest.TestCase):
    """Unit tests defined for app.py"""

    def test_return_backwards_string(self):
        """Test return backwards simple string"""
        random_string = "This is my test string"
        random_string_reversed = "gnirts tset ym si sihT"
        self.assertEqual(random_string_reversed, returnBackwardsString(random_string))

if __name__ == "__main__":
    unittest.main()
```

**Create a requirements file. requirements.txt** : dependencies for app.py 

Next, Add the apps requirements. flask==1.0.2

```
flask==1.0.2
```

**Create file buildspec.yml**: 


instructions for AWS CodeBuild to run unit tests.  Refer to Build Specification Reference for more information about the capabilities and syntax available for buildspec files.

```
version: 0.2
phases:
  install:
    runtime-versions:
      python: 3.8
  pre_build:
    commands:
      - pip install -r requirements.txt
      - python app_test.py
```

**Create a Docker File name Dockerfile**

This is a container based application so we are going to need Docker file which gives the instructions to docker on how to create a container from the app.

```
FROM python:3
# Set application working directory
WORKDIR /usr/src/app
# Install requirements
COPY requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt
# Install application
COPY app.py ./
# Run application
CMD python app.py
```

**Create a task-definition.json** file


task-definition.json: specifications for an ECS Task Definition. Note: in this file, replace the placeholder value with your AWS Account ID

```
{
    "requiresCompatibilities": [
        "FARGATE"
    ],
    "inferenceAccelerators": [],
    "containerDefinitions": [
        {
            "name": "ecs-devops-sandbox",
            "image": "ecs-devops-sandbox-repository:00000",
            "resourceRequirements": null,
            "essential": true,
            "portMappings": [
                {
                    "containerPort": "8080",
                    "protocol": "tcp"
                }             
            ]
        }
    ],
    "volumes": [],
    "networkMode": "awsvpc",
    "memory": "512",
    "cpu": "256",
    "executionRoleArn": "arn:aws:iam::<YOUR_AWS_ACCOUNT_ID>:role/ecs-devops-sandbox-execution-role",
    "family": "ecs-devops-sandbox-task-definition",
    "taskRoleArn": "",
    "placementConstraints": []
}
```

The end result of this step should be a GitHub repository containing all application, test, and deployment files.