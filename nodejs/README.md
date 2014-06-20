Node.JS / SSHD Dockerfile Image
====

Based upon google/debian:wheezy with added SSH and node.js  

As minimal as I could make it and still offer:

- git
- curl
- python
- build-essential
- supervisord
- node.js
- dropbear ssh

This is a base image and should be used to generate specific containers for  
your node application.

The latest STABLE (currently v0.10.x) version of node is automatically  
downloaded for each container built with this base.

####USAGE:
    git clone https://github.com/TheInsomniac/docker
    cd docker/nodejs
    docker build --rm -t YOURIMAGENAME .
#####Container:
_Dockerfile_:  

    FROM YOURIMAGENAME:latest
    # Set environment variables
    ENV NODE_ENV production
    # Expose PORT# for your node application
    EXPOSE PORT#
    #Set the root password for SSH logins...
    RUN echo 'root:PASSWORD' | chpasswd

Include, in the same directory as your new Dockerfile, the entire node.js
application including any subdirectories. These will be copied into the
container.  

Ensure that you have a _package.json_ file as npm will automatically install
these dependencies for you.  

If your main application is not named server.js then also ensure that you
have the following within your package.json

    "scripts": {
      "start": "node APPLICATION_NAME.js"
    }

When running the container, supervisord will automatically start dropbear ssh
and npm start your application.

If you're exposing ports (and want access to SSH), ensure that you run the
container with the -P or -p options to make these available to the host.
