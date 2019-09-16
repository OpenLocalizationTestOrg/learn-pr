Docker is a technology that enables you to deploy applications and services quickly and easily. A Docker app runs using a Docker image. A Docker image is a prepackaged environment containing the application code and the environment in which the code executes.

In the corporate scenario described earlier, you want to investigate the feasibility of packaging and running an app with Docker. You decide to build and deploy a Docker image running a test web app.

In this unit, you'll learn about the key concepts and processes involved in running a containerized app stored in a Docker image.

## Overview of Docker

Docker is a tool for running containerized apps. A containerized app includes the app and the filesystem that makes up the environment in which it runs. For example, a containerized app could consist of a database and other associated software and configuration information needed to run the app.

A containerized app typically has a much smaller footprint than a virtual machine configured to run the same app. This smaller footprint is because a virtual machine has to supply the entire operating system and associated supporting environment. A Docker container doesn't have this overhead because Docker uses the operating system kernel of the host computer to power the container. Downloading and starting a Docker image is typically much faster and more space-efficient than downloading and running a virtual machine that provides similar functionality.

You create a containerized app by building an **image** that contains a set of files and a section of configuration information used by Docker. You run the app by asking Docker to start a container based on the image. When the container starts, Docker uses the image configuration to determine what application to run inside the container. Docker provides the operating system resources and the necessary security to ensure that containers are running concurrently and remain *relatively* isolated.

> [!IMPORTANT]
> Docker does not provide the level of isolation available with virtual machines. A virtual machine implements isolation at the hardware level. Docker containers share underlying operating system resources and libraries. However, Docker ensures that one container cannot access the resources of another unless the containers are configured to do so.

You can run Docker on your desktop or laptop if you're developing and testing locally. For production systems, Docker is available for server environments, including many variants of Linux and Microsoft Windows Server 2016. Many vendors also support Docker in the cloud. For example, you can store Docker images in Azure Container Registry and run containers with Azure Container Instances.

In this unit, you'll use Docker locally to build and run an image, then upload it to Azure Container Registry and run it in an Azure Container Instance. This version of Docker is suitable for local development and testing of Docker images.

## Linux and Windows Docker images

Docker was initially developed for Linux and has expanded to support Windows. Individual Docker images are either Windows-based or Linux-based, but can't be both at the same time. The operating system of the image determines what kind of operating system environment is used inside the container.

Authors of Docker images who wish to offer similar functionality in both Linux-based and Windows-based images can build those images separately. For example, Microsoft offers Windows and Linux Docker images containing an ASP.NET Core environment that can be used as the basis for containerized ASP.NET Core applications.

Linux computers with Docker installed can only run Linux containers. Windows computers with Docker installed can run both kinds of containers. Windows accomplishes this by using a virtual machine to run a Linux system and uses the virtual Linux system to run Linux containers.

In this module, you will build and run a Linux-based image.

## Docker registries and Docker Hub

Docker images are stored and made available in *registries*. A registry is a web service to which Docker can connect to upload and download container images. The most well-known registry is Docker Hub, which is a public registry. Many individuals and organizations publish images to Docker Hub, and you can download and run these images using Docker running on your desktop, on a server, or in the cloud. You can create a Docker Hub account and upload your images there for free.

A registry is organized as a series of *repositories*. Each repository contains multiple Docker images that share a common name and generally the same purpose and functionality. These images normally have different versions identified with a tag. This mechanism enables you to publish and retain multiple versions of images for compatibility reasons. When you download and run an image, you must specify the registry, repository, and version tag for the image. Tags are text labels, and you can use your version numbering system (v1.0, v1.1, v1.2, v2.0, and so on).

Suppose you want to use the ASP.NET Core Runtime Docker image. This image is available in two versions:

- `mcr.microsoft.com/dotnet/core/aspnet:2.2`
- `mcr.microsoft.com/dotnet/core/aspnet:2.1`

Now, let's suppose you want to use the .NET Core samples Docker images. Here we have four versions available to choose from:

- `mcr.microsoft.com/dotnet/core/samples:dotnetapp`
- `mcr.microsoft.com/dotnet/core/samples:aspnetapp`
- `mcr.microsoft.com/dotnet/core/samples:wcfservice`
- `mcr.microsoft.com/dotnet/core/samples:wcfclient`

> [!NOTE]
> A single image can have multiple tags assigned to it. By convention, the most recent version of an image is assigned the tag *latest* in addition to a tag that describes its version number. When you release a new version of an image, you can reassign the *latest* tag to reference the new image.

A repository is also the unit of privacy for an image. If you don't wish to share an image, you can make the repository private. You can grant access to other users with whom you want to share the image.

## Browse Docker Hub and pull an image

Often you'll find that there's an image in Docker Hub that closely matches the type of app you want to containerize. You can download such an image and extend it with your application code.

Docker Hub contains many thousands of images. You can search and browse a registry using Docker from the command line or the Docker Hub website. The website allows you to search, filter, and select images by type and publisher. The figure below shows an example of the search page.

![Screenshot of the Docker Hub search page](../media/2-docker-hub-search.png)

You use the `docker pull` command with the image name to retrieve an image. By default, Docker will download the image tagged `latest` from that repository on Docker Hub if you specify only the repository name. Keep in mind that you can modify the command to pull different tags and from different repositories. This example fetches the image with the tag `aspnetapp` from the *mcr.microsoft.com/dotnet/core/samples:aspnetapp* repository. This image contains a simple ASP.NET Core web app.

> [!NOTE]
> The examples in this unit are intended to show the syntax of the various Docker commands. You don't need to run these commands while reading this unit. The exercises that follow this unit will guide you through working with Docker directly.

```bash
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

When you fetch an image, Docker stores it locally and makes it available for running containers. You can view the images in your local registry with the *docker image list* command.

```bash
docker image list
```

The output looks like the example below.

```console
REPOSITORY TAG IMAGE ID CREATED SIZE
mcr.microsoft.com/dotnet/core/samples   aspnetapp           6e2737d83726        6 days ago          263MB
```

You use the image name ID to reference the image in many other Docker commands.

## Run a Docker container

Use the `docker run` command to start a container. Specify the image to run with its name or ID. If you haven't `docker pull`ed the image already, Docker will do it for you.

```bash
docker run mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

In this example, the command will respond with the following message:

```console
warn: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[35]
 No XML encryptor configured. Key {d8e1e1ea-126a-4383-add9-d9ab0b56520d} may be persisted to storage in unencrypted form.
Hosting environment: Production
Content root path: /app
Now listening on: http://[::]:80
Application started. Press Ctrl+C to shut down.
```

This image contains a web app, so it's now listening for requests to arrive on HTTP port 80. However, if you open a web browser and navigate to `http://localhost:80`, you won't see the app.

By default, Docker doesn't allow inbound network requests to reach your container. You need to tell Docker to assign a specific port number from your computer to a specific port number in the container by adding the `-p` option to `docker run`. This instruction enables network requests to the container on the specified port.

Additionally, the web app in this image isn't meant to be used interactively from the command line. When we start it, we want Docker to start it in the background and just let it run. Use the `-d` flag to instruct Docker to start the web app in the background.

You can press Ctrl-C to stop the image and then restart it as shown by the following example.

```bash
docker run -p 8080:80 -d mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

The command maps port 80 in the container to port 8080 on your computer. If you visit the page http://localhost:8080 in a browser, you'll see the sample web app.

![Screenshot of the sample web app running in a browser](../media/2-sample-web-app.png)

## Containers and files

If a running container makes changes to the files in its image, those changes only exist in the container where the changes are made. Unless you take specific steps to preserve the state of a container, these changes will be lost when the container is removed. Similarly, multiple containers based on the same image that run simultaneously do not share the files in the image - each container has its own independent copy. Any data written by one container to its filesystem are not visible to the other.

It's possible to add writable volumes to a container. A volume represents a filesystem that can be mounted by the container, and is made available to the application running in the container. The data in a volume does persist when the container stops, and multiple containers can share the same volume. The details for creating and using volumes are outside the scope of this module.

It is a best practice to avoid the need to make changes to the image filesystem for applications deployed with Docker. Only use it for temporary files that can afford to be lost.

## Manage Docker containers

You can view active containers with the `docker ps` command.

```bash
docker ps
```

The output includes the status of the container. *Up* if it is running, *Exited* if it has terminated, among other values such as the command line flags specified when the image was started, and additional information. Docker lets you run multiple containers from the same image simultaneously, so each container is assigned a unique ID as well as a unique human-readable name. Most Docker commands used to manage individual containers can use either the ID or the name to refer to a specific container.

In the output below, you can see two containers. The *PORTS* field shows that the container with ID `lucid-jang` is the image running with port 80 on the Docker host mapped to port 8080 on your computer. The `youthful_heisenberg` instance is the container for the previous run of the image. The *COMMAND* field shows the command that the container ran to start the application in the image. In this case, for both containers, it is *dotnet aspnetapp.dll*. Note that the image ID for the containers is also the same because both containers are executing the same image.

```console
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
57b9587583e3        mcr.microsoft.com/dotnet/core/samples:aspnetapp   "dotnet aspnetapp.dll"   42 seconds ago      Up 41 seconds       0.0.0.0:8080->80/tcp   elegant_ramanujan
```

> [!NOTE]
> `docker ps` is a shortcut for `docker container ls`. The names of these commands are based on the Linux utilities `ps` and `ls`, which list running processes and files, respectively.

You can stop an active container with the `docker stop` command and specify the container ID.

```bash
docker stop elegant_ramanujan
```

If you run `docker ps` again, you'll see that the *lucid_jang* container is no longer present in the output. The container still exists, but is no longer hosting a running process. You can include stopped containers in the output of `docker ps` by including the `-a` flag:

```console
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
57b9587583e3        mcr.microsoft.com/dotnet/core/samples:aspnetapp   "dotnet aspnetapp.dll"   2 minutes ago       Exited (0) 21 seconds ago                       elegant_ramanujan
```

You can restart a stopped container with the `docker start` command. The main process of the container will be started anew.

```bash
docker start elegant_ramanujan
```

Typically, once a container is stopped, it should also be removed. Removing a container cleans up any resources it leaves behind. Once you remove a container, any changes made within its image filesystem are permanently lost.

```bash
docker rm elegant_ramanujan
```

You can't remove a container that is running, but you can force a container to be stopped and removed with the *-f* flag to the `docker rm` command. This is a quick way to stop and remove a container, but should only be used if the app inside the container does not need to perform a graceful shutdown.

```bash
docker container rm -f elegant_ramanujan
```

## Remove Docker images

You can remove an image from the local computer with the `docker image rm` command. Specify the image ID of the image to remove. This example removes the image for the sample web app:

```bash
docker image rm mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Containers running the image must be terminated before the image can be removed. If the image is still in use by a container, you'll get an error message like the one shown below. In this example, the error occurs because the *youthful_hesienburg* container is still using the image.

```console
Error response from daemon: conflict: unable to delete 575d85b4a69b (cannot be forced) - image is being used by running container c13165988cfe
```
