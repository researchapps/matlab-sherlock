# Matlab for Sherlock

This container is intended to run Matlab (using the hosts) and a license server also provided on the host. This is why it's relatively sparse. The original verison is from [satra/om-images](https://singularity-hub.org/containers/163/). What we are going to do is run the host's matlab inside the container. This is the approach others have taken to quickly take advantage of the matlab environments set up on their clusters. We could think about some approach that goes through the awful manual gui installation process, but I think this is a more reasonable start. First, here is a command to just do something in the container (notice in the build spec file it doesn't have a runscript, and this means if you run it, it will just shell to bash.


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

Now let's go to our image, and shell inside it. Since it doesn't have a runscript, we can do this just by running the image:

```bash
cd /scratch/users/vsochat/share
./researchapps-matlab-sherlock-master.img
No Singularity runscript found, executing /bin/sh
```

Is there matlab inside?

```bash 
Singularity.researchapps-matlab-sherlock-master.img> $ which matlab
Singularity.researchapps-matlab-sherlock-master.img> $ 
```

well of course not. It's not loaded. Let's exit, load matlab, and see if we can find it.

```bash
exit
module load matlab
./researchapps-matlab-sherlock-master.img
which matlab
```

Stil no matlab inside the container, and the reason is because the container doesn't have the path mapped with matlab. We can actually see the `$PATH` in the container has been added, and the container knows where matlab is:

```bash
echo $PATH
/share/sw/licensed/MATLAB-R2016b/bin:/opt/rh/python27/root/usr/bin:/share/sw/free/singularity/2.2.1/bin:/home/vsochat/.local/bin:/share/sw/srcc/bin:/usr/lib64/qt-3.3/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/opt/pdsh/bin:/opt/rocks/bin:/opt/rocks/sbin:/opt/dell/srvadmin/bin:/home/vsochat/bin:/home/vsochat/.local/bin
```
so it's just an issue that share is not mapped. We can do this easily by binding. Let's exit the container and try a bind. We will issue a command to list the files at the bind point in the container.

```bash
singularity exec -B /share:/share researchapps-matlab-sherlock-master.img ls /share
PI sw
```

That's great! Now do we find matlab?

```bash
singularity exec -B /share:/share researchapps-matlab-sherlock-master.img which matlab
/share/sw/licensed/MATLAB-R2016b/bin/matlab
```

Now let's run a script:

```bash
echo "disp('Hello World')" >> hello.m
singularity exec -B /share:/share researchapps-matlab-sherlock-master.img matlab -nodisplay -nosplash -nodesktop -r hello.m
```

Note that the above takes forever to do, since it's starting up Matlab, etc. It was very slow for me to run.


There it is! So now we can give some input script to matlab as you would normally, but it will run inside the container, with whatever dependency libraries for matlab are required.
