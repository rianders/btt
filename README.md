# Bergen Tech Training Student Workshop for Self-Driving RC Cars

We'll be using the (FOOCars project([https://github.com/fubarlabs/foocars] from Fair Use Building and Research Labs

The students will have 10 - 14 cars to collect data with.

There are internet bandwidth issues so the normal internet train with a Jupyter Notebook will have to be done locally.

There are somethings that need to be confirmed and then we can make the process of uploading data, training, and placing the weights back on the cars conveniently.

* Did we set up key based SSH access?
  * If so this allows us to script getting the data from the car to training stations.
  
* Confirm how many training statiosn we can have.
  * Make sure they are on the same network
  
The cars are configured to use mDNS which mean we can find each car easily.
 * But we'll need a list of all the cars so we can script the resolts
 
The Training system can have a local Jupyter notebok or do training from the command line. 
 
One thing that can would make it helpful is if the students could use a web page on the training machine and upload, train, then download their results.


notes:
Another concern is to make sure there is enough space on the car pi.
```
#list images
docker image ls

#find unused images
docker image ls -f dangling=true

#Remove/Prune unused images
docker image prune

#remove all
docker image prune -a


```

One way to make getting the data out of the Docker image is to make sure to run with a volume. Then use 

```export_data.sh
#!/bin/bash

# Set the name of the volume and the container image
VOLUME_NAME="foocarsdata"
IMAGE_NAME="debian:10"

# Create a temporary container to access the volume data
CONTAINER_ID=$(docker run -d -v $VOLUME_NAME:/foocars/cars/chiaracer --entrypoint /bin/sleep $IMAGE_NAME 1d)

# Create a backup of the data in the volume using tar
docker exec $CONTAINER_ID sh -c "cd /foocars/cars/chiaracer && tar czf data_backup.tar.gz data/"

# Copy the backup file to the host
docker cp $CONTAINER_ID:/foocars/cars/chiaracer/data_backup.tar.gz .

# Stop and remove the temporary container
docker stop $CONTAINER_ID

# Print the location of the backup file
echo "Data backup saved as data_backup.tar.gz in the current directory."

```
