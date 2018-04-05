# Traefik Wordpress

This is an example on how to use [Træfik](https://traefik.io/) with a wordpress site, or a node web app.
This is with a Lets Encrypt setup, handled by Træfik.

I really need a nice name, but it's hard.

## Getting started
First you need docker, and that's pretty much it. Then just follow these steps:

1. `docker network create web` Creates the web-network that we will use across docker containers.
2. `cd treafik` & `docker-compose up -d`, this starts the load balancer.
4. `cd wordpress` & `docker-compose up -d`, this starts the wordpress website.

You can have all the sites you like, this should be no problem, just update the labels for docker.
