
Cannot start box if Harvester is running as Docker container

Clean up docker: Remove stopped containers and dangling images (REPOSITORY <NONE>, TAG <NONE>)

docker container prune

docker rmi $(docker images -f "dangling=true" -q)


Remove by repository:tag:

docker image rm debian:buster