FROM jenkins/inbound-agent:latest
USER root
RUN apt-get update && apt-get install -y lsb-release && \
   curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
   https://download.docker.com/linux/debian/gpg && \
   echo "deb [arch=$(dpkg --print-architecture) \
   signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
   https://download.docker.com/linux/debian \
   $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list && \
   apt-get update && apt-get install -y docker.io && \
   apt-get clean && rm -rf /var/lib/apt/lists/*  && \
   usermod -aG docker jenkins
USER jenkins

# docker build -t codelabmaster/jenkins-agent-docker . 
# docker push codelabmaster/jenkins-agent-docker
