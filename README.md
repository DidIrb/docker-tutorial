# Docker Tutorial

Docker, Kubernetes, Containers, Images, DevOps, They are all interesting topics and I am going to cover them in this tutorial, Feel free to use this as a tutorial.

**Feel free to make corrections as need be.**

Lets get started:

**Docker** is a software platform that allows you to build, test, and deploy applications quickly

**Containers** are standardized units of packages that have everything the software needs to run, this includes *systems tools, code and runtime* 

**Docker Images** contain application code, libraries, tools, dependencies and other files needed to make an application run

Table of Content

Installing Docker 

- Visit - https://docs.docker.com/engine/install/ 
- Mac and Linux are quite easy to install just follow the instructions.
- Windows Installation will need to enable ws2 to do that visit [Microsoft](https://learn.microsoft.com/en-us/windows/wsl/install) for instructions
- Once done Download the file from [dockers](https://docs.docker.com/desktop/install/windows-install/) official site then install it
- Restart your computer and Open Docker Desktop

## Practice

To create a simple rest service to practice:
Open whatever directory you like
open cmd and paste

    mkdir nodejs

    npm init

    npm install express cors nodemon

Create an index.js file then copy and paste 

    const express = require("express");
    const cors = require("cors");

    const app = express()

    app.use(cors())

    app.get('/', (req, res) => {
        res.json([
            {
                'id':"1",
                'title':"Title 1",
            },
            {
                'id':"2",
                'title':"Title 2",
            },
            {
                'id':"3",
                'title':"Title 3",
            },
            
        ])
    })

    app.listen(4000, () => {
        console.log("Listening on port 4000")
    })

The example just returns a json response when the endpoint is hit

we are going to practice deploying a rest service using docker so to check it out 

<!-- [click here](./nodejs/Readme.md) -->

### Docker images

Let us add a parent image in this instance let us add a node image to docker to do so 

open powershell and paste teh command

    docker pull node

Docker images contain multiple layers, docker files are used to create docker images.

Installing images from docker hub
Visit [dockerhub](https://hub.docker.com/search?q=node)

### Docker file

This is a text document that contains all the commands a user could call on the command line to assemble an image.

An image has many layers and we use a docker file to define the commands that we use to build the image we want here is how you do so

Create a file called *dockerfile*

In this file you will write your instructions

Defining the Parent Image 

    FROM node:20-alpine 

Specify Working Directory
    
    WORKDIR /app 

Copy your code

    COPY ./nodejs .

***./nodejs = File to copy from***
***. = File to copy to - this is relative to the working directory***

Run the command to install dependencies using command RUN

    RUN npm install 

EXPOSE PORT FOR DOCKER DESKTOP

    EXPOSE 4000

Command to run the code in docker

    CMD ["npm", "run", "dev"]

Build the image
open terminal
cd into directory the dockerfile is in
run command

    docker build -t myapp .

#### DOCKERIGNORE

In order to ignore the files you do not want ship into the image you will create a **.dockerignore** file and add what you don't want
- create .dockerignore file
- write node_module

### DOCKER COMMANDS

listing images

    docker images

running and naming the container of the image - 
**docker run --name container image**

    docker run --name myapp_c1 myapp

listing processes in docker

    docker ps

stopping a process

    docker stop myapp_c2

mapping ports from docker to hosts computer using *-p*
***3000:5000** - 3000 -> Computers port 5000 -> Dockers port*
*-d detatches from terminal 
-p sets --name - specifies the name of the container*


    docker run --name myapp_c3 -p 3000:5000 -d myapp3

List all containers

    docker ps -a

Stopping a container

    docker stop containername

Starting a container

    docker start containername

### DELETE 

Deleting an image

    docker image rm myapp

Sometimes you cannot delete an image that is being used by a container and if you want to forcefully delete the image you run the command

    docker image rm myapp -f

Deleting a container

    docker container rm myapp_c1

To delete all images, containers, and volumes you use the command 

    docker system prune -a

Layer cashing - This basically cashes layers so that when you are building an image to build it quicker. to do so you us **-t**

    docker build -t myapp3 .

This will save you on time.


#### DOCKER VOLUMES

I will get back to this later

#### DOCKER COMPOSE

Docker compose is a tool for defining and running multi-container applications, for example you have a 2 tier app , that has a backend and a front end you can use compose to run these applications


To run the compose in the root folder that contains your code bases 

- Create a file **dockercompose.yaml**


    Version of docker compose to use
    version: '3.8'
    services:
    nodejs:
        build: ./nodejs
        container_name: api_c
        ports:
        - '4000:4000' # Linking ports
        volumes:
        - ./nodejs:/app # Mapping the local directory to one in image for syncing
        - /app/node_modules # Prevents node modules from being deleted 


Open the directory the docker-compose file is in and the run command

    docker compose up

Now let us also add a front end to ship

we are going to use vite here 

    npm create vite@latest
    cd client
    npm install
    npm run dev

Add this code inside the app.tsx file

    interface Item {
        title: string;
        id: number;
    }
    
    const [count, setCount] = useState(0)
    const [items, setItems] = useState<Item[]>([]);
    useEffect(() => {
        fetch('http://localhost:4000/')
        .then(res => res.json())
        .then(data => setItems(data))
    })

    ///  Inside the Return add 
    <div>
    <h3>All Items</h3>
        {items && items.map(item => (
        <div key={item.id}>{item.title}</div>
        ))}
    </div>

Create a docker file inside the file in this case

    FROM node:20-alipine

    WORKDIR /app

    COPY package.json .

    RUN npm install

    COPY . .

    EXPOSE 5173

    CMD ["npm", "run", "dev"]

Define the one for react 

    client:
        build: ./client
        container_name: client_c
        ports:
        - '5173:3000'
        stdin_open: true
        tty: true
        
Now we have the docker.yaml file in the root of the directory, you will add the client code as well in order to create it, this is how the the file will look like

    version: "3.8"
    services:
    nodejs:
        build: ./nodejs
        container_name: api_c
        ports:
        - '4000:4000' 
        volumes:
        - ./nodejs:/app
        - /app/node_modules
    client:
        build: ./client
        container_name: client_c
        ports:
        - '5173:3000'
        stdin_open: true
        tty: true

Run

    docker compose up