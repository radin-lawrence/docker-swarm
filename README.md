# Creating docker image from flask application

Flask is a small and lightweight Python web framework that provides useful tools and features that make creating web applications in Python easier. It gives developers flexibility and is a more accessible framework for new developers since we can build a web application quickly using only a single Python file.

Here we are going to create a docker image from an flask application. Let's move to the process



## Installation

If you need to download docker to the EC2 instance, then follow this:

~~~sh
sudo yum install docker -y
sudo systemctl restart docker.service
sudo systemctl enable docker.service
sudo systemctl status docker.service
sudo usermod -a -G docker ec2-user
~~~

Please exit from the instance and login back to reflect the above changes

## Create directory and required files

We have to create  a project directory  and create the  flask app file inside the project directory

~~~sh
mkdir flask-app
cd flask-app/
touch app.py requirements.txt Dockerfile
~~~

##  Add code to app.py

 In this step, we make a small web application inside a Python file, which will display some information on the browser.

In your flask-app directory, open a file named app.py for editing,


~~~sh
vim app.py
~~~
Add simple flask application:

~~~
from flask import Flask
app = Flask(__name__)

@app.route("/")
def index():
  return "<h1><center> simple flask application - version 1</center></h1>"

if __name__ == "__main__":
  app.run(host='0.0.0.0',port=5000)
~~~

##  Add to requirements.txt

Here we are adding the modules required for this flask application
~~~sh
vim requirements.txt
~~~
Add:
~~~
flask
~~~

##  Create Dockerfile 

Now we will edit the Dockerfile for building our custom docker image from flask application. Open "Dockerfile" 
~~~sh
vim Dockerfile
~~~

And we add these:
~~~
FROM alpine:3.8
    
ENV FLASK_PATH <your_file_path>

ENV FLASK_USER <Your_user_name>

RUN adduser  -h $FLASK_PATH -s /bin/sh -D  $FLASK_USER

WORKDIR  $FLASK_PATH

COPY  .  .

RUN chown -R $FLASK_USER:$FLASK_USER $FLASK_PATH

RUN apk update && apk add python3 --no-cache

RUN pip3 install -r requirements.txt

USER $FLASK_USER

EXPOSE 5000

ENTRYPOINT ["python3"]

CMD ["app.py"]
~~~

> Note: Replace <your_file_path> with your project direcory path and with user name


Then, lets start to build the docker image,

##  Build docker image and push to hub

Let's build the image, 
~~~sh
docker image build -t flasktest:1 .
~~~

Now, let's add this custom docker image to our docker hub. First we need to sign up in [docker hub](https://hub.docker.com/) and then follow this:

~~~sh
docker login
~~~
Enter your credentials and you will get a login succeeded message/result once done

![image](https://user-images.githubusercontent.com/100775027/165773373-664a1e29-4970-4e8c-b46f-86c431ffe43a.png)

We will rename the custom image (flasktest:1) to desired one, here I rename it to:

~~~sh
docker image tag  flasktest:1 <user_name>/flaskapptest:custom
docker image tag  flasktest:1 <user_name>/flaskapptest:latest
~~~

> Note: please use your docker hub username on here <user_name>



Now, we can push the image to docker hub

~~~sh
docker image push <user_name>/flaskapptest:custom
docker image push <user_name>/flaskapptest:latest
~~~

![image](https://user-images.githubusercontent.com/100775027/165774663-9262053f-6d5e-431b-af10-9d78f813d0ca.png)


Now we can pull this image for creating docker container!

## To check: 



 Let's try to pull this image in [play-with-docker](https://labs.play-with-docker.com/) :

~~~sh
docker image pull <user_name>/flaskapptest:latest
~~~

![image](https://user-images.githubusercontent.com/100775027/165773614-e7eee5c1-5ec9-4cdc-9ee0-90794d1e73bb.png)

Launch thr conatiner from the image <user_name>/flaskapptest:latest

~~~sh

docker container run --name web -d -p 80:5000 <user_name>/flaskapptest:custom
~~~

![image](https://user-images.githubusercontent.com/100775027/165774163-839edf54-2ddb-48f4-b755-312aa8350cdf.png)

Loads the public ip on browser,

![image](https://user-images.githubusercontent.com/100775027/165774343-5617e416-b201-444a-bdc5-b4d4758c7745.png)


## Conclusion

This is how a secure docker custom image is created and pushed to docker hub. Please contact me when you encounter any difficulty error while using this terrform code. Thank you and have a great day!


 ### ⚙️ Connect with Me
<p align="center">
<a href="https://www.linkedin.com/in/radin-lawrence-8b3270102/"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a>
<a href="mailto:radin.lawrence@gmail.com"><img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/></a>



