Description:
In this scenario, we'll explore how you can ignore certain files from ending up inside a Docker image that can introduce security risks. This scenario also investigates how you can reduce the build time by ignoring which files are sent to the Docker Build Context.

Step 1 of 3
$ cat Dockerfile
$ docker build -t password
$ docker run password ls /app(This will include the passwords file)

$ echo passwords.txt >> .dockerignore
$ docker build -t nopassword 
$ docker run nopassword ls /app

Step 2
It's wise to ignore .git directories along with dependencies that are downloaded/built within the image such as node_modules. These are never used by the application running within the Docker Container and just add overhead to the build process.
