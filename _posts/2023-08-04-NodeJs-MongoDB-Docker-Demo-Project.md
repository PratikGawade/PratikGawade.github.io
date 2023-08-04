---
title: Docker Demo Project
tags: [Docker, Nodejs, Project, Docker-compose]
style: fill
color: danger
description: Learn and build a multi-containarized application.
---

Source: [TechWorld with Nana](https://youtu.be/3c-iBn73dDE)

![](https://developers.redhat.com/sites/default/files/styles/article_feature/public/blog/2014/05/homepage-docker-logo.png?itok=zx0e-vcP)



## Introduction:
In the world of modern application development, containerization has become a game-changer. Docker provides a seamless way to package applications and their dependencies, ensuring consistent deployment across different environments. In this tutorial, we'll walk through the process of building a simple user profile app using Docker, Node.js with Express, and MongoDB. By the end of this guide, you'll have a clear understanding of how to containerize your applications, manage data storage with Docker volumes, and troubleshoot common issues.

## Prerequisites:
Before we dive into the details, make sure you have the following tools installed on your system:

1. Docker: The containerization platform that will help us package our app and its dependencies.
2. Docker Compose: A tool for defining and running multi-container Docker applications.

## Application Architecture:
Our user profile app consists of three main components:

1. Frontend: The user interface, built using index.html.
2. Backend: The server logic, powered by Node.js and Express.
3. Database: MongoDB, which stores user profile data.\

## Clone the Repository:
To get started, clone the repository containing the demo app:
```
  https://gitlab.com/nanuchi/techworld-js-docker-demo-app
```

## Setting Up MongoDB and MongoDB Express:
We'll begin by pulling the necessary Docker images for MongoDB and MongoDB Express. Open your terminal and execute the following commands:
```
docker pull mongo
docker pull mongo-express
```

## Creating the Docker Image for the Application:
Next, we need to create a Docker image for our user profile application. To do this, we'll create a Dockerfile in the root directory of your app:
```
# Use an official Node.js runtime as the base image
FROM node:14

# Set the working directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json to the working directory
COPY package*.json ./

# Install app dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose the port that the app runs on
EXPOSE 3000

# Define the command to run your app
CMD ["node", "index.js"]

```

## Containerizing the Application:
With the Dockerfile in place, we're ready to containerize our application. Open your terminal and run the following commands:

docker build -t user-profile-app .

## Creating a Docker Network:

```
docker network create user-profile-network

```

## Running MongoDB with Docker:
Start the MongoDB container and link it to the created network:
```
docker run -d --name user-profile-db --network user-profile-network mongo

```
## Running MongoDB Express:
Launch MongoDB Express to manage the database:
```
docker run -d --name mongo-express --network user-profile-network -e ME_CONFIG_MONGODB_SERVER=user-profile-db -p 8081:8081 mongo-express
```

## Running the User Profile Application:
Finally, run the user profile app container and link it to the network:
```
docker run -d --name user-profile-app --network user-profile-network -e MONGO_URL=mongodb://user-profile-db:27017/user-profile-db -p 3000:3000 user-profile-app

```

## Running the Application with Docker Compose:
Now, let's define a docker-compose.yml file to simplify the process of running our app:
```
version: '3'
services:
  app:
    image: user-profile-app
    ports:
      - "3000:3000"
    networks:
      - user-profile-network
    environment:
      - MONGO_URL=mongodb://mongo:27017/user-profile-db

  mongo:
    image: mongo
    networks:
      - user-profile-network
    volumes:
      - mongo-data:/data/db

  mongo-express:
    image: mongo-express
    networks:
      - user-profile-network
    ports:
      - "8081:8081"
    environment:
      - ME_CONFIG_MONGODB_URL=mongodb://mongo:27017/
networks:
  user-profile-network:
volumes:
  mongo-data:
```

## Conclusion:
By manually leveraging Docker's capabilities, you've successfully built a simple user profile app using Node.js, Express, and MongoDB. This approach provides you with a deeper understanding of the inner workings of Docker containers and how they interact. As you continue your exploration of containerization and application deployment, consider delving into more advanced Docker features and best practices. Your newfound knowledge will empower you to confidently manage complex application environments. Happy coding!

## Create a Callback

_Alright, enough talk, lets create a callback!_

First, open up your Chrome Developer Console (Windows: Ctrl + Shift + J)(Mac: Cmd + Option + J) and type the following function declaration into your console:

```javascript
function doHomework(subject) {
  alert(`Starting my ${subject} homework.`);
}
```

Above, we’ve created the function `doHomework` . Our function takes one variable, the subject that we are working on. Call your function by typing the following into your console:

```javascript
doHomework('math');
// Alerts: Starting my math homework.
```

Now lets add in our callback — as our last parameter in the `doHomework()` function we can pass in `callback`. The callback function is then defined in the second argument of our call to `doHomework()`.

```javascript
function doHomework(subject, callback) {
  alert(`Starting my ${subject} homework.`);
  callback();
}

doHomework('math', function() {
  alert('Finished my homework');
});
```

As you’ll see, if you type the above code into your console you will get two alerts back to back: Your ‘starting homework’ alert, followed by your ‘finished homework’ alert.

But callback functions don’t always have to be defined in our function call. They can be defined elsewhere in our code like this:

```javascript
function doHomework(subject, callback) {
  alert(`Starting my ${subject} homework.`);
  callback();
}
function alertFinished(){
  alert('Finished my homework');
}
doHomework('math', alertFinished);
```

This result of this example is exactly the same as the previous example, but the setup is a little different. As you can see, we’ve passed the `alertFinished` function definition as an argument during our `doHomework()` function call!

## A real world example

Last week I published an article on how to Create a Twitter Bot in 38 lines of code. The only reason the code in that article works is because of Twitters API. When you make requests to an API, you have to wait for the response before you can act on that response. This is a wonderful example of a real-world callback. Here’s what the request looks like:

```javascript
T.get('search/tweets', params, function(err, data, response) {
  if(!err){
    // This is where the magic will happen
  } else {
    console.log(err);
  }
})
```

- `T.get` simply means we are making a get request to Twitter
- There are three parameters in this request: `‘search/tweets’`, which is the route of our request, `params` which are our search parameters, and then an anonymous function which is our callback.

A callback is important here because we need to wait for a response from the server before we can move forward in our code. We don’t know if our API request is going to be successful or not so after sending our parameters to search/tweets via a get request, we wait. Once Twitter responds, our callback function is invoked. Twitter will either send an `err` (error) object or a `response` object back to us. In our callback function we can use an `if()` statement to determine if our request was successful or not, and then act upon the new data accordingly.