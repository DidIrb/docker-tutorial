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

    git clone --depth 1 --filter=blob:none https://github.com/DidIrb/docker-tutorial.git --branch master --single-branch nodejs

OR

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

we are going to practice deploying a rest service using docker so to check it out [click here](./nodejs/Readme.md)

Ignore files you do not want

Let us practice Deploying a simple rest service using Docker to do so you will install parent images - example node is a parent image.

Installing images from docker hub
Visit [dockerhub](https://hub.docker.com/search?q=node)

Ignoring files you do not want to send to git
**Dockerignore** is a file similar to gitignore and ignores files you do not want to send when creating the image.
In thi

In our example you do not want to delete 

