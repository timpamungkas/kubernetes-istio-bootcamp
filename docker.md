# Docker
[Docker website](https://docker.com/)

# Basic Docker Hands-On

```bash
# run hello-world
docker run hello-world

# run busybox:stable
docker run busybox:stable

# run nginx 
docker run nginx

# list all containers
docker ps -a

# remove containers
docker rm -f [container-id or container-name]

# run nginx : exposing container port 80 to host port 8888, give it name smiling_whale
docker run -d -p 8888:80 --name smiling_whale nginx

# Go to bash shell inside smiling_whale container
docker exec -it smiling_whale bash

# Create another nginx with name dancing_octopus and expose port 80 to host port 8899
docker run -d -p 8899:80 --name dancing_octopus nginx

# Go to bash shell inside dancing_octopus container
docker exec -it dancing_octopus bash

# Copy file from host to smiling_whale, folder /usr/share/nginx/html/
# Make sure you go to folder where atlantis.html located, or define full path to atlantis.html
docker cp atlantis.html smiling_whale:/usr/share/nginx/html/

# Copy file from host to dancing_octopus, folder /usr/share/nginx/html/
# Make sure you go to folder where atlantis.html located, or define full path to atlantis.html
docker cp atlantis.html dancing_octopus:/usr/share/nginx/html/

# Copy file from container smiling_whale to host
docker cp smiling_whale:/usr/share/nginx/html/whale.html whale_copy.html

# Mount folder from host into container smiling_whale /usr/share/nginx/html/
# Please adjust the folder d:/... into your own local folder
docker run -d -p 8888:80 --name smiling_whale -v d:/my-courses/nginx-html:/usr/share/nginx/html/ nginx

# Mount folder from host into container dancing_octopus /usr/share/nginx/html/
# Please adjust the folder d:/... into your own local folder
docker run -d -p 8888:80 --name dancing_octopus -v d:/my-courses/nginx-html:/usr/share/nginx/html/ nginx

```

# Building Docker Image

Download and extract scripts (zip file) from last section of the course, on lecture with title **Resources & References**  

The java code and docker files are located under folder `kubernetes-istio\kubernetes-istio-java-code`, and in this part we use `release-1.0.0` sub-folder

```bash
# Build java from gradle (note the separator, use \ or / according to your operating system)
# This is for windows OS (\)
# From kubernetes-istio\kubernetes-istio-java-code\release-1.0.0\devops.blue
.\gradlew clean bootJar

# Build docker image (note the dot as last character)
docker build --tag devops-blue:1.0.0 .

# Login to docker registry (just do this once)
docker login

# Tag local docker image
docker tag devops-blue:1.0.0 [your-docker-username]/devops-blue:1.0.0

# Push docker image to registry
docker push [your-docker-username]/devops-blue:1.0.0
```

To run the image, exposing at port 8888

```bash
docker run -d -p 8888:8111 --name my-devops-blue devops-blue:1.0.0
```