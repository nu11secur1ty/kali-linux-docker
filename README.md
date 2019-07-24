# Official Kali Linux Docker
This Kali Linux Docker image provides a minimal base install of the latest version of the Kali Linux Rolling Distribution.
There are no tools added to this image, so you will need to install them yourself. 
For details about Kali Linux metapackages, check https://www.kali.org/news/kali-linux-metapackages/

---------------------------------------------------------------------------------------------------
Docker Hub: https://hub.docker.com/r/nu11secur1ty/kali-linux-docker

# REQUIRES EXPERIMENTAL TO BE TURNED ON
Due to --squash being passed to the docker daemon, if experimental features
aren't turned on in your daemon, the build.sh script will fail.

On Kali, this is done via /etc/docker/daemon.json having the following contents:

```
{
    "experimental": true
}
```

Note: This is only a requirement for us at Offensive Security to reduce the image size when we push a new image to Docker Hub.
If you're building for personal use, you can remove the `--squash` option in build.sh

Tue May 21 13:59:06 EDT 2019

# Setting up a Kali Linux Docker Image

Obviously, to get this running, you need to install Docker. For Docker on OSX you can use brew, while for most other distributions, you can install it using your local package manager. Once installed and set up, it’s just a matter of pulling our image from the Docker repository:

- Example of running the docker and Metasploit
```bash
nu11secur1ty:~ # docker pull kalilinux/kali-linux-docker
nu11secur1ty:~ # docker run -t -i kalilinux/kali-linux-docker /bin/bash
root@7e2a35940eff:/# apt-get update && apt-get install metasploit-framework
root@7e2a35940eff:/# service postgresql start
root@7e2a35940eff:/# ss -ant
root@7e2a35940eff:/# msfdb init
root@7e2a35940eff:/# msfconsole
```
# Building Your Own Kali Linux Docker Image

If you want to build your own Kali images rather than use our pre-made ones, we’ve made it easy with the following script hosted on Kali Linux Docker on Github. These images are best built on a Linux system or any other OS that can debootstrap.


```bash
#!/bin/bash
# Install dependencies (debootstrap)
sudo apt-get install debootstrap
# Fetch the latest Kali debootstrap script from git
curl "https://gitlab.com/kalilinux/packages/debootstrap.git;a=blob_plain;f=scripts/kali;hb=HEAD" > kali-debootstrap &&\
sudo debootstrap kali ./kali-root http://http.kali.org/kali ./kali-debootstrap &&\
# Import the Kali image into Docker
sudo tar -C kali-root -c . | sudo docker import - kalilinux/kali &&\
sudo rm -rf ./kali-root &&\
# Test the Kali Docker Image
docker run -t -i kalilinux/kali cat /etc/debian_version &&\
echo "Build OK" || echo "Build failed!"
```
![](https://github.com/nu11secur1ty/kali-linux-docker/blob/master/screenshot_Metasploit/Screenshot%20from%202019-07-24%2020-19-24.png)

# Have fun with your Kali Docker images! :)
