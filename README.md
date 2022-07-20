# docker_ubuntu_python_keras


# Overview
Here is sample minimal code for running keras-ocr in a flask docker image.

Follow each step and run each command in your terminal. Instructions that start with "$" are likely terminal commands. Other instructions will be text to copy into files.


## New Project Folder
It is recommended that you create a project-folder and then do your work in that folder("directory"). Run this line of code to create a project folder and then make that folder the "current working directory" where your terminal opera"my_project_folder" is arbitrary, you can name your project anything you like.
```
$ mkdir my_project_folder; cd my_project_folder
```


## Check Docker Memory Use 
Check if docker is still using memory in any area:
```
sudo docker system df
```

## Remove Old Files (optional)
```
$ sudo docker system prune; sudo docker image prune; sudo docker volume prune; sudo docker container prune
```

## First Setup Bash Script
```
$ touch Dockerfile; touch requirements.txt; touch readme.txt; mkdir app; cd app; touch app.py; cd ..
```

## readme.md / readme.txt 
Having a readme or instructions file is helpful for documentation and clear communication. You can put the contents of this guide into that readme file. 
```
# Instructions and Guide
```

## requirements.txt
Add this text to your file. This specific minimal project has no required text, no required packages, but some docker applications require a 'requirements.txt' file even if that file is blank. (e.g. AWS-lambda-function docker-containers)
```
Flask
keras-ocr
```

# Dockerfile
Add this text to your file. 
```
# Get docker base image
FROM ubuntu:20.04

# ?
RUN printf '#!/bin/sh\nexit 0' > /usr/sbin/policy-rc.d

# Update ubuntu linux software and packages
RUN apt-get update -y

# fix txdata glitch
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends tzdata

# Install and update pip and python-dev 
RUN apt-get install -y python3-pip python3-dev
RUN pip3 install --upgrade pip

# Install setuptools
RUN pip3 install setuptools
RUN pip3 install scikit-build
RUN pip3 install tensorflow

# Install opencv-python
# RUN apt-get install ffmpeg libsm6 libxext6 -y
# RUN pip3 install opencv-python
RUN apt-get install libgl1 -y
RUN apt-get install libgtk2.0-dev -y

# Import requirements.txt into docker
COPY ./requirements.txt /requirements.txt

# Set working directory
WORKDIR /

# Install python requirements
RUN pip3 install -r requirements.txt

# Copy all files in project directory into docker
COPY . /

# Specify the executable to run
ENTRYPOINT [ "python3" ]

# The function of this docker is to run the app.py
CMD [ "app/app.py" ]
```

# app.py
Add this text to your file. 
```
from flask import Flask
import keras_ocr 

pipeline = keras_ocr.pipeline.Pipeline()
images = [
    keras_ocr.tools.read(url) for url in [
        'https://storage.googleapis.com/gcptutorials.com/examples/keras-ocr-img-1.jpg',        
        'https://storage.googleapis.com/gcptutorials.com/examples/keras-ocr-img-2.png'
    ]
]

# Terminal Print: data arrays
print(images[0])
print(images[1])

# Variables to store data arrays

image0 = images[0]
image1 = images[1]

# store words in lists/arrays
text_1 = []
text_2 = []

# build NLP/OCR pipeline
prediction_groups = pipeline.recognize(images)

# run prediction on image 1
predicted_image_1 = prediction_groups[0]
for text, box in predicted_image_1:
    # terminal print
    print(text)

    # add text to list of words
    text_1.append( text )

# run prediction on image 2
predicted_image_2 = prediction_groups[1]
for text, box in predicted_image_2:
    # terminal print
    print(text)

    # add text to list of words
    text_2.append( text )  

# Flask "app" (note, in AWS maybe named differently)
app = Flask(__name__)

# main function
@app.route("/")
def index():
    return f"""
    <h1>Hello World!</h1>
    <h6>{image0}</h6>
    <h6>{image1}</h6>
    <h6>{text_1}</h6>
    <h6>{text_2}</h6>
    <p> |O...O| </p>
    """

if __name__ == "__main__":
    app.run(debug=True, host='0.0.0.0')
```

## Build your Docker image
Add this text to your file. 

"project_abc" is an arbitrary project name, you can use any name you like.

The final period "." is not a typo:
```
sudo docker build -t project_abc .
```

#### Successful builds output these lines at end:
```
Successfully built ############
Successfully tagged ###:latest
```

## Run your Docker container
```
$ sudo docker run --name flaskapp -v$PWD/app:/app -p5000:5000 project_abc:latest
```

## See you results in a browser
Now the application can be accessed at 

http://localhost:5000 or  http://0.0.0.0:5000/

#### Successful run should print a display to a browser.
```
Hello, World... etc.
```

## Remove Old Files 
```
$ sudo docker system prune; sudo docker image prune; sudo docker volume prune; sudo docker container prune
```


## Remove Old Files  (optional)
Check if docker is still using memory in any area:
```
sudo docker system df
```





## Sources and Resources
- use ubuntu, install docker
- https://docs.docker.com/engine/install/ubuntu/
- https://www.freecodecamp.org/news/how-to-remove-all-docker-images-a-docker-cleanup-guide/ 
- https://www.educba.com/docker-entrypoint/
- https://stackoverflow.com/questions/46247032/how-to-solve-invoke-rc-d-policy-rc-d-denied-execution-of-start-when-building 
- https://linuxhint.com/docker-entrypoint-beginner-guide/ 
- https://www.gcptutorials.com/post/extract-text-from-images-using-keras-ocr-in-python 
- https://www.codethebest.com/modulenotfounderror-no-module-named-skbuild-solved/ 
- https://stackoverflow.com/questions/55313610/importerror-libgl-so-1-cannot-open-shared-object-file-no-such-file-or-directo 
- https://www.tensorflow.org/install/
- https://hub.docker.com/r/tensorflow/tensorflow/ 



## Sample Output from Browser
![Image](https://github.com/lineality/docker_ubuntu_python_keras/blob/main/bowser_outout_keras_mvp.png)

