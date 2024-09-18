---
description: Integrating Salesforce and Heroku through External Services
---

# Introduction

Heroku is a Platform as a Service (PaaS) that offers managed computing and data services for deploying and running applications written with open languages. It supports [Node.js, Python, Ruby, Java, Go, and PHP.](https://devcenter.heroku.com/start)\
\
In this challenge, you will deploy a Node.js application to Heroku and integrate it with Salesforce via [External Services](https://help.salesforce.com/s/articleView?id=sf.external\_services.htm\&type=5).\
\
The application provides an API using the [OpenAPI 3 specification](https://swagger.io/specification/). This API is for Lumina Solar, a fictional solar energy company, and returns metrics on solar energy consumption and production by system. Salesforce will execute this API from either Flow or Apex, depending on your preference.\


## Prerequisites

Please make sure you have the following before starting the workshop:

* [Node.js LTS (> v20)](https://nodejs.org)
  * You can install it using:
    * [Official Binaries](https://nodejs.org/en/download/)
    * [nvm](https://github.com/nvm-sh/nvm)
    * [Volta](https://volta.sh/) (Recommended)
* [Git](https://git-scm.com/downloads) / [GitHub](https://github.com/join)
* [Google Chrome](https://www.google.com/chrome/) or [Chromium](https://www.chromium.org/Home)
* A Salesforce Org (Developer Edition/Trailhead Playground/Scratch org). You can either use an existing org or [create a new org](https://developer.salesforce.com/signup).
* [Heroku Account](https://signup.heroku.com/) (Ask me for access)
  * [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#download-and-install)
* [Visual Studio Code](https://code.visualstudio.com/download) (Optional)
