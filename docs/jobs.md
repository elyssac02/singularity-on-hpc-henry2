# Running jobs with Singularity

## Interactive
To run interactively, and 'go into the image' rather than just do a command, use 'shell':
```bash
bsub -Is -q sif -W 10 tcsh
module load singularity
singularity shell /usr/local/usrapps/[hpcproject]/singularity_images/image.sif
```
If you do that on HPC, do 'cd' and 'pwd' around. Notice you can see files in the current directory where you called singularity, and your home directory, but you will not see for instance /usr/local/usrapps.

## Serial
```bash
#!/bin/csh
#BSUB -W 0:30
#BSUB -n 1
#BSUB -J singularity
#BSUB -q sif
#BSUB -R "rusage[mem=5000]"
#BSUB -oo out_sing
#BSUB -eo err_sing

module load singularity

# General syntax
#singularity exec /usr/local/usrapps/[hpcproject]/singularity_images/image.sif <command> <arg-1> <arg-2> ... <arg-N>

# Example command
singularity exec /usr/local/usrapps/[hpcproject]/singularity_images/image.sif python test.py
```

If you receive some errors (e.g., 'error while loading shared libraries'), you may need to use the option `--cleanenv` so that the host environment variables are not passed into the container:
 ```bash
 singularity exec --cleanenv /usr/local/usrapps/[hpcproject]/singularity_images/image.sif python test.py
 ```
