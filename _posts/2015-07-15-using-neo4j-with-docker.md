---
layout: post
title: Using Neo4j as a Docker container
tags: neo4j, docker
excerpt: some tips for running Neo4j as a Docker container
---

### {{page.title}}

After working with Neo4j for some projects, I am convinced that I would rather keep different data sets isolated from each other. Normally this can be done by copying database folders into Neo4j installation folder etc. However, I find dockerizing Neo4j to be a lot more practical to separate my data sets. With Neo4j running inside a Docker container, it gets very easy to switch between data sets (or even use them at the same time).

Below are some tips on how to get started and configure Neo4j in a Docker container.

#### selecting an image
A quick search on Docker Hub for Neo4j will bring so many results to pick from. I personally use [tpires/neo4j](https://registry.hub.docker.com/u/tpires/neo4j/) image, which happens to be one of the images mentioned in Neo4j developer [site](http://neo4j.com/developer/docker/). This post will contain commands that are specific to this image. However, it should be straight forward to apply these commands to any other images out there.

Download the image with the following command, if you haven\'t so already:

<script src="https://gist.github.com/artsince/e20fa667d35a2671e645.js?file=first.sh"></script>

#### authentication
By version 2.2, neo4j introduced token-based authentication which is enabled by default. That is actually not too bad, but it might be frustrating for your development and test environment where you might want to automate creating and deleting neo4j instances.

You can pass authentication credentials as an environment variable when you create your neo4j image. To set username and password, use the following sample command.

<script src="https://gist.github.com/artsince/e20fa667d35a2671e645.js?file=auth-tokens.sh"></script>

Username and password information are stored in the `dbms/auth` file in the Neo4j installation directory. As the container is created, this file is modified to include your credentials before Neo4j starts.

Alternatively, you can bypass the authentication altogether with the following command.

<script src="https://gist.github.com/artsince/e20fa667d35a2671e645.js?file=auth-none.sh"></script>

With this option, `dbms.security.auth_enabled=false` line is appended to the conf/neo4j-server.properties file which disables token-based authentication.

#### Data persistence
You can designate a directory in your host environment to store your data locally and point it to your Docker container to be used by Neo4j.
Neo4j keeps data in the `data/graph.db` folder and for this particular image we are using, the path for Neo4j is `var/lib/neo4j`. So, the following command will map your local directory to the Neo4j database directory.

<script src="https://gist.github.com/artsince/e20fa667d35a2671e645.js?file=volumes.sh"></script>

<br /><br/>

In summary, it is pretty straight-forward to containerize Neo4j in your local development environment. I find this approach to be a very practical way to keep my projects isolated and to quickly run some queries without tainting my existing data.
