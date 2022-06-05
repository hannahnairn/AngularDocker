# Angular Docker Application
> This document will go over how I made a sample angular docker application.

## Create the Application
The Angular application was created using the Angular CLI.
- To begin with run in a terminal `npm install` to get all the node modules
- Next create the angular project with `ng new project` command
- Move into the projects directory with `cd project`
- Then you can build you application with `ng build`. This will create a ./dist fodler with all the build files. At this point you can also run `ng serve` to run your project without the use of Docker. This will be included in the dockerfile later on and was mainly done to check the project and ng commands.

## Create Files
Next in the base directory of your project (project/) create a Dockerfile. For this project I created a multi-stage docker build. I chose this option as the container will result in being smaller and actually setting up and running the container will be more automated. The dockerfile get spilt into parts or steps, in this case two steps. This means when docker is building the image the result of a previous step can be used in another. 

As shown below the first step here will be to compile the code and the second step will be running the code. This means you don't need to run `ng build` before creating the image using the docker command.

```
#Step 1: Build
FROM node:12.7-alpine AS build
WORKDIR /usr/src/app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

#Step 2: Run
FROM nginx:1.17.1-alpine
COPY nginx.conf /etc/nginx/nginx.conf
COPY /dist/project /usr/share/nginx/html
```


Then once the dockerfile is written a a nginx configuration file. In the same base directory as the Dockerfile a `nginx.conf` is needed. The one in this project had default structure as it was only a simple application and the Dockerfile (step 2) calls the nginx offical image and builds from it - this does most of the work in setting it up.

## Creating the Docker Image and Container
The last steps were to create the docker image, done through entering this `docker build -t project-image . `  command in . 

On successful completion, a docker container can then be made and run. By entering this command in the terminal `docker run --name project-container -d -p 4200: 80 project-image`.

You should then be able to access the application on localhost:4200 in any web browser.
