FROM google/debian:wheezy

MAINTAINER The Insomniac <manxam2k@hotmail.com>

ENV DEBIAN_FRONTEND noninteractive

# Install curl, python, build-essential, git, dropbear sshd, and openssh-client
# for scp support. This should be everything required to build a nodejs program
# except for node itself, of course...
RUN \
  apt-get update -y && \
  apt-get install --no-install-recommends -y -q curl python build-essential git && \
  apt-get install --no-install-recommends -y -q dropbear openssh-client

ENV HOME /root

# supervisor installation
# create directory for child images to store configuration in
# create conf files for supervisord, sshd, and node
RUN \
  apt-get -y install supervisor && \
  mkdir -p /var/log/supervisor && \
  mkdir -p /etc/supervisor/conf.d && \
  rm /etc/supervisor/supervisord.conf

ADD supervisor/supervisord.conf /etc/supervisord.conf
ADD supervisor/dropbear.sv.conf /etc/supervisor/conf.d/dropbear.sv.conf
ADD supervisor/node.sv.conf /etc/supervisor/conf.d/node.sv.conf

#echo '[supervisord]\nnodaemon=true\nlogfile=/root/supervisord.log\npidfile=/var/run/supervisord.pid\n\n[include]\nfiles=/etc/supervisor/conf.d/*.conf\n' > /etc/supervisord.conf && \
#echo '[program:dropbear]\ncommand=/usr/sbin/dropbear -F\nredirect_stderr=true\n' > /etc/supervisor/conf.d/dropbear.sv.conf && \
#echo '[program:node]\ncommand=/nodejs/bin/npm start\nredirect_stderr=true\n' > /etc/supervisor/conf.d/node.sv.conf

# clean up after ourselves
RUN \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# install the latest node.js stable binary
ONBUILD RUN \
  VERSION=$(curl -L -s http://nodejs.org/dist/latest/ | egrep -o '[0-9]+\.[0-9]+\.[0-9]+' | tail -n1) && \
  mkdir /nodejs && \
  curl http://nodejs.org/dist/v$VERSION/node-v$VERSION-linux-x64.tar.gz | tar xvzf - -C /nodejs --strip-components=1

# add node to our path
ONBUILD ENV PATH $PATH:/nodejs/bin

# Set the working directory, add the package.json, npm install, and add
# remaining
ONBUILD WORKDIR /app
ONBUILD ADD package.json /app/
ONBUILD RUN npm install
ONBUILD ADD . /app
ONBUILD ENTRYPOINT ["supervisord", "-c", "/etc/supervisord.conf"]

# Expose 22 for SSH
EXPOSE 22
