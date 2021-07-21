# Base docker compose command
docker-compose up -d
docker-compose ps
docker-compose logs
docker stats
# Pause and Unpause
docker-compose pause
docker-compose unpause
# Stop container 
docker-compose stop
# If you want to remove the containers, networks, and volumes associated with this containerized environment, use the down command:
docker-compose down
# Remove base image
docker image rm nginx:alpine