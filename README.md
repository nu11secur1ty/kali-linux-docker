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

- Example of running the docker and Metasploit + vim + git
```bash
nu11secur1ty:~ # docker pull nu11secur1ty/kali-linux-docker
nu11secur1ty:~ # docker run -t -i nu11secur1ty/kali-linux-docker /bin/bash
root@7e2a35940eff:/# apt update
root@7e2a35940eff:/# apt dist-update
root@7e2a35940eff:/# apt update && apt install metasploit-framework
root@7e2a35940eff:/# apt update && apt install git
root@7e2a35940eff:/# apt update && apt install vim
root@7e2a35940eff:/# service postgresql start
root@7e2a35940eff:/# ss -ant
root@7e2a35940eff:/# msfdb init
root@7e2a35940eff:/# msfconsole
```
# Other software:
- sqliv2

```bash
git clone https://github.com/nu11secur1ty/sqliv2.git
cd sqliv2
```
. usage
link:[sqliv2](https://github.com/nu11secur1ty/sqliv2)

-----------------------------------------------------------------------------------------------

- nu11secur1ty pack

. Get:
```bash
git clone https://github.com/nu11secur1ty/nu11secur1ty.git
```
link:[nu11secur1ty](https://github.com/nu11secur1ty/nu11secur1ty)


######################################################
----------------------------------------------------------------------------------------------

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

- - - Last Release:

![](https://github.com/nu11secur1ty/kali-linux-docker/blob/master/screenshot_Metasploit/last-release.png)


***OTHER SOFTWARE DOCUMENTATION, INSTALLATION AND REMOVING A DOCKER IMAGES***
-------------------------------------------------------------------------------------

-------------------------------------------------------------------------------------------------------------------
# How do I SSH into a running container
***There is a docker exec command that can be used to connect to a container that is already running.*** 
 
   - Use docker ps to get the name of the existing container
   - Use the command `docker exec -it <container name> /bin/bash` to get a bash shell in the container
   - Generically, use `docker exec -it <container name> <command>` to execute whatever command you specify in the container.

# How do I run a command in my container?

The proper way to run a command in a container is: `docker-compose run <container name> <command>`. For example, to get a shell into your web container you might run docker-compose run web `/bin/bash`

To run a series of commands, you must wrap them in a single command using a shell. For example: `docker-compose run 
<name in yml> sh -c '<command 1> && <command 2> && <command 3>'`

In some cases you may want to run a container that is not defined by a docker-compose.yml file, for example to test a new container configuration. Use docker run to start a new container with a given image: `docker run -it <image name> <command>`

The docker run command accepts command line options to specify volume mounts, environment variables, the working directory, and more.

# Getting a shell for build/tooling operations

Getting a shell into a build container to execute any operations is the simplest approach. You simply want to get access to the `cli` container we defined in the compose file. The command `docker-compose -f build.yml run cli` will start an instance of the `phase2/devtools-build` image and run a bash shell for you. From there you are free to use `drush`, `grunt` or whatever your little heart desires.


# Running commands, but not from a dedicated shell

Another concept in the Docker world is starting a container to run a single command and allowing the container stop when the command is completed. This is great if you run commands infrequently, or don't want to have another container constantly running. Running your commands on containers in this fashion is also well suited for commands that don't generate any files on the filesystem or if they do, they write those files on to volumes mounted into the container.

The `drush` container defined in the example `build.yml` file is a container designed specifically to run drush in a single working directory taking only the commands as arguments. This approach allows us to provide a quick and easy mechanism for running any drush command, such as `sqlc`, `cache-rebuild`, and others, in your Drupal site quick and easily.

There are also other examples of a `grunt` command container similar to `drush` and an even more specific command container around running a single command, `drush make` to build the site from a make/dependency file.

-----------------------------------------------------------------------------------------------------------------------------

The webapp folder on the host will be mounted into the container's apache root

# Contributing

+ Report Issues
+ Open a Pull Request


------------------------------------------------------------------------------------------------------


# Docker provides a single command that will clean up any resources — images, containers, volumes, and networks — that are dangling (not associated with a container):

```bash
docker system prune
```


# To additionally remove any stopped containers and all unused images (not just dangling images), add the -a flag to the command:

```bash 
docker system prune -a
```

# Remove one or more specific images

Use the docker images command with the -a flag to locate the ID of the images you want to remove. This will show you every image, including intermediate image layers. When you've located the images you want to delete, you can pass their ID or tag to docker rmi:

- **List:**
```bash
docker images -a
```
- **Remove:**
```bash 
docker rmi Image Image
```
# Remove dangling images

Docker images consist of multiple layers. Dangling images are layers that have no relationship to any tagged images. They no longer serve a purpose and consume disk space. They can be located by adding the filter flag, -f with a value of dangling=true to the docker images command. When you're sure you want to delete them, you can use the docker images purge command:

**NOTE**
```txt
If you build an image without tagging it, the image will appear on the list of dangling images because it has no association with a tagged image. You can avoid this situation by providing a tag when you build, and you can retroactively tag an images with the docker tag command.
```
- **List:**
```bash
docker images -f dangling=true
```
- **Remove:**
```
docker images purge
```
# Removing images according to a pattern

You can find all the images that match a pattern using a combination of docker images and grep. Once you're satisfied, you can delete them by using awk to pass the IDs to docker rmi. Note that these utilities are not supplied by Docker and are not necessarily available on all systems:

- **List:**
```bash
docker images -a |  grep "pattern"
```

- **Remove:**
```bash
docker images -a | grep "pattern" | awk '{print $3}' | xargs docker rmi
```
# Remove all images

All the Docker images on a system can be listed by adding -a to the docker images command. Once you're sure you want to delete them all, you can add the -q flag to pass the Image ID to docker rmi:

- **List:**
```bash
docker images -a
```
- **Remove:**
```bash
docker rmi $(docker images -a -q)
```
# Remove a container upon exit

If you know when you’re creating a container that you won’t want to keep it around once you’re done, you can run docker run --rm to automatically delete it when it exits.

- **Run and Remove:**
```bash
 docker run --rm image_name
```
# Remove all exited containers

You can locate containers using docker ps -a and filter them by their status: created, restarting, running, paused, or exited. To review the list of exited containers, use the -f flag to filter based on status. When you've verified you want to remove those containers, using -q to pass the IDs to the docker rm command.

- **List:**
```bash
docker ps -a -f status=exited
```
- **Remove:**
```bash
docker rm $(docker ps -a -f status=exited -q)
```
# Remove containers using more than one filter

Docker filters can be combined by repeating the filter flag with an additional value. This results in a list of containers that meet either condition. For example, if you want to delete all containers marked as either Created (a state which can result when you run a container with an invalid command) or Exited, you can use two filters:

- **List:**
```bash
docker ps -a -f status=exited -f status=created
```
- **Remove:**
```bash
docker rm $(docker ps -a -f status=exited -f status=created -q)
```
# Remove containers according to a pattern

You can find all the containers that match a pattern using a combination of docker ps and grep. When you're satisfied that you have the list you want to delete, you can use awk and xargs to supply the ID to docker rmi. Note that these utilities are not supplied by Docker and not necessarily available on all systems:

- **List:**
```bash
docker ps -a |  grep "pattern”
```
- **Remove:**
```bash
docker ps -a | grep "pattern" | awk '{print $3}' | xargs docker rmi
```
# Stop and remove all containers

You can review the containers on your system with docker ps. Adding the -a flag will show all containers. When you're sure you want to delete them, you can add the -q flag to supply the IDs to the docker stop and docker rm commands:

- **List:**
```bash
docker ps -a
```
- **Remove:**
```bash
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```
# Removing Volumes
Remove one or more specific volumes - Docker 1.9 and later

Use the docker volume ls command to locate the volume name or names you wish to delete. Then you can remove one or more volumes with the docker volume rm command:

- **List:**
```bash
docker volume ls
```
- **Remove:**
```bash
docker volume rm volume_name volume_name
```
# Remove dangling volumes - Docker 1.9 and later

Since the point of volumes is to exist independent from containers, when a container is removed, a volume is not automatically removed at the same time. When a volume exists and is no longer connected to any containers, it's called a dangling volume. To locate them to confirm you want to remove them, you can use the docker volume ls command with a filter to limit the results to dangling volumes. When you're satisfied with the list, you can remove them all with docker volume prune:

- **List:**
```bash
docker volume ls -f dangling=true
```
- **Remove:**
```
docker volume prune
```
# Remove a container and its volume

If you created an unnamed volume, it can be deleted at the same time as the container with the -v flag. Note that this only works with unnamed volumes. When the container is successfully removed, its ID is displayed. Note that no reference is made to the removal of the volume. If it is unnamed, it is silently removed from the system. If it is named, it silently stays present.

- **Remove:**
```bash
docker rm -v container_name
```
# Conclusion

This guide covers some of the common commands used to remove images, containers, and volumes with Docker. There are many other combinations and flags that can be used with each. For a comprehensive guide to what's available, see the Docker documentation for docker system prune, docker rmi, docker rm and docker volume rm. If there are common cleanup tasks you'd like to see in the guide, please ask or make suggestions in the comments.

------------------------------------------------------------------------------------------------------------------------

# Vulnerabilities

![](https://github.com/nu11secur1ty/opensuse-apache-docker/blob/master/Screenshot%20from%202018-05-12%2020-31-11.png)

# Packages providing

![](https://github.com/nu11secur1ty/opensuse-apache-docker/blob/master/Screenshot%20from%202018-05-12%2020-32-12.png)

# Good luck to everyone! ;)
# Have fun with your Kali Docker image! :)
