
# look docker system space
docker system df

# Run container 
docker container run --name www -d -p 8000:80 nginx

# What happens to the disc
docker system df

# Added file
docker exec -ti www \
  dd if=/dev/zero of=test.img bs=1024 count=0 seek=$[1024*100]

docker system df

# Search file
find /var/lib/docker -type f -name test.img

# Docker stop
docker stop www

# Prune
docker container prune

# Check
docker system df

# Delete all containers
docker rm -f $(docker ps –aq)
docker container rm -f $(docker container ls -aq)