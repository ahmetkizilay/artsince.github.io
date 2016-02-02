---
layout: post
title: Dockerizing a Neo4j instance with an extension
tags: neo4j, docker, elasticsearch
excerpt: Setting up neo4j docker image to work with an unmanaged extension
---

### {{page.title}}

In this post I will demonstrate how to setup a Neo4j container with an existing database and a custom extension that synchronizes data with an Elastic Search instance. The extension I am using is a fork of [this project](https://github.com/neo4j-contrib/neo4j-elasticsearch) on GitHub.

To get started, pull the 2.3.2 tag of Neo4j image. Some functionality I will explain later in the post depend on this version (and up).

<script src="https://gist.github.com/artsince/111e145b2250db2257be.js?file=one.sh"></script>

#### Adding volumes
Volumes are a way to persist data across several invocations of a container by mapping directories from the host file system to the container. For this Neo4j instance, I will need to add three volume definitions.

1. data volume: This is the `data` folder inside neo4j installation which containing the `graph.db` directory which actually holds the database files.
2. plugins volume: the elastic search extension has some jar files that should go to the plugins folder of Neo4j.
3. extension volume: This is my custom volume that contains a script file to be run before Neo4j starts. Note that this mapping will create a new directory in the Neo4j container. Therefore, it is important to select a directory name that will not break anything in the installation.

#### Before-Start Hook
With 2.3.2 Neo4j image, I can use `EXTENSION_SCRIPT` environment variable to specify a script file to execute before Neo4j starts. The Elastic Search extension needs two parameters to be set in `neo4j.properties` file, so I will create a script that concatenates two lines to the end of the file.

<script src="https://gist.github.com/artsince/111e145b2250db2257be.js?file=two.sh"></script>

I know that Neo4j is installed in `/var/lib/neo4j` and it is OK to create a directory named `extension`. So, my command looks like this: 
<script src="https://gist.github.com/artsince/111e145b2250db2257be.js?file=three.sh"></script>

As a bonus, I can add service definitions for Neo4j and Elastic Search in a docker-compose file for easier networking and maintainability.

<script src="https://gist.github.com/artsince/111e145b2250db2257be.js?file=four.yml"></script>

Now, running docker-compose with `--x-networking` flag will create two containers with `container_name` as their host names.

As a second bonus, I can modify my extension script to make sure the Elastic Search container starts before Neo4j does, so that I will not get any Connection Refused errors on startup.

<script src="https://gist.github.com/artsince/111e145b2250db2257be.js?file=five.sh"></script>

<br/>
<br/>
In this post, I showed how to setup a Neo4j instance with access to an Elastic Search service over the Docker network. For any questions or comments, feel free to get in touch by writing a comment below or sending me a tweet  [@ahmetkizilay](https://twitter.com/ahmetkizilay). Enjoy!
