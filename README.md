# Matlab for Sherlock

This container is intended to run Matlab (using the hosts) and a license server also provided on the host. This is why it's relatively sparse. The original verison is from [satra/om-images](https://singularity-hub.org/containers/163/).

# Accessing the Container
If you are a Stanford folk, this container is provided for your use and absuse at `/scratch/users/vsochat/share` 

```bash
cd /scratch/users/vsochat/share
ls
```

# Generating the image
Here is the command I used to get the image, but I had to do this locally because Sherlock has an older version of singularity installed (before I added pull)

```bash
singularity pull shub://researchapps/matlab-sherlock
```

You could do that on the cluster, given the admins install the newer version. This is how you load singularity on Sherlock:

```bash
module load singularity
```

...and let's load matlab too

```bash
module load matlab
which matlab
/share/sw/licensed/MATLAB-R2016b/bin/matlab
```

Now we are going to run the host's matlab inside the container. This is the approach others have taken to quickly take advantage of the matlab environments set up on their clusters. We could think about some approach that goes through the awful manual gui installation process, but I think this is a more reasonable start. First, here is a command to just do something in the container (notice in the build spec file it doesn't have a runscript, and this means if you run it, it will just shell to bash.

```bash
singularity exec researchapps-sherlock-matlab-master.img echo "which matlab"
```
