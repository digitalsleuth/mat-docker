# Name: MAT (Mobile Analysis Toolkit)
# Website: https://github.com/digitalsleuth/mat-docker/
# Description: 
# Author: Corey Forman
# License: MIT License (https://github.com/digitalsleuth/mat-docker/blob/master/LICENSE)
# Notes: 

# The purpose of this docker image is to encapsulate MAT, the Mobile Analysis Toolkit.
#
# There are multiple ways to use this docker. The two easiest are:
#
# As a simple, single-use container: docker run --rm -it -v <local_directory>:/home/mat/files digitalsleuth/mat-distro /bin/bash
# Where <local_directory> is a path in your host you would like to share/access from within the docker
#
# Or as a continuously running container over ssh: docker run -d --name mat --privileged -p 22:22 digitalsleuth/mat-distro
# To access this with an X11 interface, use: ssh -X mat@0.0.0.0
# Or you can remove the -X to simply connect over ssh.
#
# NOTE: 
#   -- The privileged flag is required when running certain applications, including Cutter.
#   -- If you are planning to run this docker on a cloud-based environment, you should ensure you change the default password
#

FROM ubuntu:20.04
LABEL description="MAT is a Mobile Analysis Toolkit for performing mobile forensics"
LABEL maintainer="Corey Forman (github.com/digitalsleuth)"

USER root
RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update && \
    apt-get upgrade -y && \
    apt-get install wget gnupg git -y && \
    wget -nv -O - https://repo.saltstack.com/py3/ubuntu/20.04/amd64/3001/SALTSTACK-GPG-KEY.pub | apt-key add - && \
    echo deb [arch=amd64] http://repo.saltstack.com/py3/ubuntu/20.04/amd64/3001 focal main > /etc/apt/sources.list.d/saltstack.list && \
    apt-get update && \
    apt-get install salt-minion -y && \
    git clone https://github.com/digitalsleuth/mat-salt /srv/salt && \
    salt-call -l info --local state.sls mat.addon pillar='{"mat_user": "mat"}' && \
    rm -rf /srv && \
    rm -rf /var/cache/salt/* && \
    rm -rf /root/.cache/* && \
    unset DEBIAN_FRONTEND

ENV TERM linux
WORKDIR /home/mat

RUN mkdir /var/run/sshd
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
