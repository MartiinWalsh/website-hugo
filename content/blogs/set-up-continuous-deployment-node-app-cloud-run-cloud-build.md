---
title: "How to Set Up Continuous Deployment for Your Node App with Cloud Run and Cloud Build"
date: 2023-03-14T19:53:33+05:30
draft: false
author: "Martin Walsh"
tags:
  - Node
  - GCP
  - Docker
  - Cloud Run
image: /images/cover.jpg
description: ""
toc: 
---

Unlock the power of automation for your Node application! In this article, learn how to set up a Cloud Run service with continuous deployment using Cloud Build, streamlining your workflow and boosting productivity.

Experience seamless updates as I show you how to automatically redeploy your application each time new code is pushed to a specified branch in your repository. Curious about the benefits of using Cloud Run? Check out [this article](https://medium.com/google-cloud/why-i-definitively-switched-from-cloud-functions-to-cloud-run-635d03f1eb4d).
## Prerequisites

**1. Node Application:** Ensure that your Node application has a package.json file with the necessary fields declared, as shown below:
``` json
{
  "name": "Example",
  "description": "",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "engines": {
    "node": ">=12.0.0"
  },
  "author": "",
  "license": "",
  "dependencies": {
    "": ""
  }
}
```
**2. Basic Web Server:** Since Cloud Run requires your container to be accessible via HTTP requests, you need to set up a basic web server that listens on a specified port. The following example demonstrates a simple Express.js web server:
``` javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  const name = process.env.NAME || 'World';
  res.send(`Hello ${name}!`);
});

const port = parseInt(process.env.PORT) || 8080;

app.listen(port, () => {
  console.log(`Hello World: listening on port ${port}`);
});
```
**3. Dockerfile:** Create a Dockerfile to define the Docker image required for your Cloud Run service. The example Dockerfile below demonstrates how to define the image, including the application code and dependencies:
``` dockerfile
# Install dependencies
FROM node:12-slim as dependencies
WORKDIR /app
COPY package.json ./
RUN npm install
COPY . .

# Build staging image
FROM dependencies
RUN npm run build
EXPOSE 3000
CMD npm run start
```
>Ensure that the run command on the last line matches the start script defined in your package.json file.

## Setting Up Cloud Run and Cloud Build

**1. Create Cloud Run Service:** Navigate to the Cloud Run product in Google Cloud Platform (GCP). Click on “Create Service”, then choose “Continuously deploy new revisions from a source repository” and click “Set Up Cloud Build”.

**2. Configure Cloud Build:** In the Cloud Build Configuration, select your repository provider (Bitbucket, Github, etc.) and choose the repository where your application is hosted.

![](/images/cloud1.webp)

**3.** Next, select the branch you wish to have continuous deployments from, and specify the Dockerfile location from the prerequisites. Then save your Cloud Build configuration settings.

![](/images/cloud2.webp)

**5.** Customise Service Settings: Configure the remaining settings for your Cloud Run service according to your specific requirements, including authentication, container, connections, and security.

**6.** Create the Service: Once you have configured all the necessary settings, click “Create”. Your service will now be redeployed automatically whenever you push changes to the specified branch.