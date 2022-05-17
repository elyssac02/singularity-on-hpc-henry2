# singularity-on-hpc-henry2
This is a repository with instructions for using Singularity on NC State University's HPC Henry2.

## Using Singularity

### Official documentation for Singularity:
* https://sylabs.io/guides/3.7/user-guide/quick_start.html

### Other helpful documents
* https://www.nas.nasa.gov/hecc/support/kb/converting-docker-images-to-singularity-for-use-on-pleiades_643.html
* https://www.nas.nasa.gov/hecc/support/kb/running-a-singularity-container-image-on-pleiades_638.html
* https://researchcomputing.princeton.edu/support/knowledge-base/singularity
* https://www.chpc.utah.edu/presentations/images-and-pdfs/containers21s.pdf

## Creating a Singularity image on HPC

First, create a folder to contain your Singularity images: 
```bash
cd /usr/local/usrapps/[hpcproject]
mkdir singularity_images
cd singularity_images
```

To get an existing Singularity image:
```bash
module load singularity
singularity pull library://pathto/singularityimage
```

Or to create an image from an existing Docker image (do this on a login node because it pulls from dockerhub):
```bash
module load singularity
singularity build image.sif docker://pathto/dockerimage
```

Or per [this website](https://researchcomputing.princeton.edu/support/knowledge-base/singularity):
```bash
module load singularity
singularity pull docker://pathto/dockerimage
```
Note that `pull` will download the container and create the sif file and `build` does the same but names the sif file and has more options.

### Modifying contents in the image 
You can pull from Singularity Library or build from Dockerhub from Henry2, but you cannot create or modify the 'contents' of a Singularity image without having root permissions, so you need create or modify images (i.e., build the container itself) on some other computer on which you have root permissions.

If you need to modify the contents of the Singlarity image (e.g., code in a script), you will need to modify it on your personal computer, save the Docker image, then convert to Singularity on HPC:
```bash
# On personal computer
# A) If changes are made to scripts or packages in an existing container:
docker ps -a # display a list of launched containers, obtain container ID
docker commit [CONTAINER_ID] image-modified # commit changes to container to a new image
# Next, run B below

# B) If no changes are made to scripts or packages, only need this code (i.e., don't run A):
docker images # to see what the image ID is
docker save -o image-modified.tar [IMAGE_ID] # Save a copy of image
# Move .tar to HPC (I put it here: /usr/local/usrapps/[hpcproject]/singularity_images)

# Next, to convert the Docker image to a Singularity image on HPC:
cd /usr/local/usrapps/[hpcproject]/singularity_images
module load singularity 
singularity build image-modified.sif docker-archive://image-modified.tar
```

## Deploying a Singularity container on Henry2
All containers must be run in sif queue, or they won't work. That is because Singularity requires some information to be written to local disk (scratch on the compute node), and sif queue includes only appropriately 'formatted' nodes. Note...that means you may accidentally request a node with resources not found in the sif queue.  

Currently, the sif queue contains:  
cores and memory per node  
16 cores 56GB      1 nodes  
16 cores 72GB    11 nodes  
16 cores 64GB    23 nodes  
20 cores 128GB   7 nodes  

For examples of running interactive jobs and of sample submission scripts, please see [jobs.md](https://github.com/elyssac02/singularity-on-hpc-henry2/blob/main/docs/jobs.md).
