---
layout: default
title: WordPressReact
permalink: /reactwp/
---

### Quickstart guide for setting up a headless WordPress site with a React front end and a chatbot
##### This is a 'quick and dirty' how-to for setting up web application with a React front end that pulls content stored in a (headless) WordPress instance and has a basic chatbot, all in a single site. It's a template for getting all the components installed and communicting. 

#### Tools Used:
* backend: [WordPress](https://wordpress.com/) for content management
* frontend: [Next.js/React](https://nextjs.org/) React web framework
* chatbot: [Rasa](https://rasa.com/) conversational AI

The first step is installing [Docker](https://www.docker.com/), this will containerize your WordPress project and handle all the dependencies and downloads needed.

```bash
# Update package list
sudo apt update && sudo apt upgrade -y

# Install Docker
sudo apt install docker.io -y

# Enable and start Docker service
sudo systemctl enable docker
sudo systemctl start docker

# Verify Docker installation
docker --version

# Install Docker Compose
sudo apt install docker-compose -y

# Verify Docker Compose installation
docker-compose --version
```
Once Docker is installed you can use this sample [docker-compose.yml](https://github.com/skenny24/portfolio/blob/main/sample-docker-compose.yml) file, add it to a new headless-wordpress/ directory and use it to start up the WordPress instance.

```bash
# Create project directory
mkdir headless-wordpress
cd headless-wordpress

# Add the docker-compose file (sample linked above) and then start docker
docker-compose up -d
```
Once your WordPress instance is running you can navigate to the admin page ([http://localhost:8000/wp-admin](http://localhost:8000/wp-admin) if running locally)  
then go to settings->permalinks set to 'Post name' and save

<div style="display: flex; justify-content: space-between;">
  <img src="/assets/images/wordpress_settings.png" alt="wordpress settings" style="width: 50%;"/>
</div>


Install and activate the [WPGraphQL plugin](https://www.wpgraphql.com/) needed for exposing a GraphQL API for WordPress. This is the component that allows React to communicate with the WordPress content and render it to the front end. 

Go to http://localhost:8000/graphql to test the GraphQL API.

#### Install React/Next.js for the front-end--it will be formatted with react and populated with WordPress data

Ensure you have [Node.js](https://nodejs.org/en/) and [npm](https://www.npmjs.com/) installed, then create a new Next.js app

```
npx create-next-app@latest react-frontend
cd react-frontend
```
Also, you'll want to add the [ChatWidget.js file](https://github.com/skenny24/portfolio/blob/main/portfolio-frontend/src/components/ChatWidget.js) to react-frontend/src/components so that it can be added to the main application page.js

Now we're ready to set up and run the front-end server. First you can get a copy of the [package.json](https://github.com/skenny24/portfolio/blob/main/portfolio-frontend/package.json) that has the packages needed, and add it to the react-frontend directory, this will ensure the proper packages are added when you run the installation and then fire up the server:

```bash
npm install
npm run dev
```
---

### SETUP FOR RASA: 
install a virtual environment for the rasa bot (may need to train an initial model--have screenshots of these q's?)

```bash
python -m venv rasa-venv
source rasa-venv/bin/activate
pip install --upgrade pip setuptools wheel
pip install rasa
rasa init 
```
This gets everything set up to start/restart, then you can use the sample bot in a shell:
```bash
# interactive terminal
rasa shell
# serve nlu model via http api
rasa run --enable-api
```
once you've verified the bot works, expose rasa for web (so our react app/website can display it)
```bash
cd chatbot
rasa run --enable-api --cors "*" --debug
```
---
Shutting down the WordPress instance (and removing all containers). This will save space and allow a clean restart:

```bash
sudo docker stop $(sudo docker ps -q)
sudo docker rm $(sudo docker ps -a -q)
```
