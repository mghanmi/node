#!/bin/bash
FROM google/cloud-sdk
RUN curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash -
RUN apt-get -y update
RUN curl -fSsL https://deb.nodesource.com/setup_lts.x | bash -
RUN apt install nodejs
RUN apt-get install openjdk-11-jre
