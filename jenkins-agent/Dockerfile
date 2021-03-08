#!/bin/bash
FROM google/cloud-sdk
RUN curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash -
RUN apt-get -y update
RUN curl -fSsL https://deb.nodesource.com/setup_lts.x | bash -
RUN apt install nodejs
RUN apt install openjdk-8-jre-headless default-jdk
