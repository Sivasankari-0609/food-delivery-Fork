FROM jenkins/jenkins:lts

USER root

# Install Git + Docker CLI
RUN apt-get update && \
    apt-get install -y git docker.io && \
    apt-get clean

USER jenkins