---
title: Run `flempar` on an any system with Docker
author: ["Niek Tettero", "Frederik Heylen"]
featured_image: '/images/c_feature5.png'
banner: '/images/banner3.jpg'

# Summary for listings and search engines
summary: Run `flempar` on an any system with Docker. 

# Link this post with a project
projects: []

# Date published
date: '2023-03-30T00:00:00Z'

# Date updated
lastmod: '2023-03-31T00:00:00Z'

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: true

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image: 
  caption: ''
  focal_point: ''
  placement: 2
  preview_only: false

# tags:
#  - Academic
#  - 开源

# categories:
#  - Demo
#  - 教程
---

### TLDR
Run `flempar` on an any system, without having to download R or Rstudio with two simple commands:

```
docker pull datamarinier/flempar
docker run --rm -ti -e PASSWORD=yourpassword -p 8787:8787 datamarinier/flempar
```
### For whom is this blog?

This blog is for anyone who would like to use the `flempar` package without having to download all the dependencies on their machine. This can be achieved by using Docker and running only two simple commands. 

Docker is a tool that allows users to deploy and run applications securely and efficiently by using 'containers'. These containers contain everything for an application to work, such as the software, libraries, and other dependencies. As a result, these containers run the same way on any machine, avoiding compatibility issues. For our application, Docker allows you to run an RStudio session in your browser. So you don’t even need to download R or Rstudio to run this solution. We installed the proper packages and dependencies so you can get started using `flempar` immediately.

I will walk you through the installation of Docker and how to run the container. For more advanced users, I’ll show how to build an image from scratch so that you can include any other software, dependencies, or packages necessary for your project.

### Prerequisites
To get started, first install Docker. Go to https://docs.docker.com/get-docker/ and follow the instructions. The general steps are:

- Download the Docker installer from the Docker website. Note that there are different installers for Windows, Mac, and Linux.
- Run the installer and follow the prompts to install Docker.
- Once the installation is complete, you can open the Docker Desktop app to start using Docker.

You do not need any experience with Docker or any other software. Following this blog should be enough to get you started. If you experience any problems with installing Docker, please refer to the [original documentation](https://docs.docker.com/). If you are experiencing issues with installing Docker, check at the end of this tutorial for a troubleshooter for some common problems.

### Docker hub
To get started immediately, you can pull the image from [Docker Hub](https://hub.docker.com/r/datamarinier/flempar). You can do this by opening a terminal. In Windows you can do this by typing CMD in the menu’s search bar. To open a terminal on Mac, go to the launchpad and search for terminal, or search ‘terminal’ in the spotlight search bar, which you can open with ⌘ + space. In the terminal, run: 

```
docker pull datamarinier/flempar
```

In this case, the following command will download the latest version of the `flempar` image. 

{{< figure src="/images/Afbeelding1.png" title="" >}}

### Running containers

There are two approaches to running a Docker container. The first one is to use the Docker Desktop app. In this app, you can simply run the image. Once the image is running, it appears in the container tab. 

{{< figure src="/images/Afbeelding2.png" title="" >}}
{{< figure src="/images/Afbeelding3.png" title="" >}}

Click on the running container to see the password that is generated. Next, go to your browser and type http://localhost:8787/ in the search bar. The final step is to type in your username and password. The username is *rstudio*, and the password you find in the terminal of the running container.

{{< figure src="/images/Afbeelding4.png" title="" >}}

The second approach is to run the image using your terminal. The command docker images shows all the images that have been built. 

```
docker run --rm -ti -e PASSWORD=yourpassword -p 8787:8787 datamarinier/flempar
```

Note that this command includes some flags. These flags have different functions and you can add more if needed. Here are some examples:

- -it tells docker to open an interactive container instance.
- -d is used to run a container in detached mode (in the background)
- -e
- -- rm is used to automatically remove the container after we close docker, but the container’s data is not deleted.

Now your container is running, you can access it through your browser. The username stays the same (*rstudio*), but the password is now set to the password you specified in the command. 

{{< figure src="/images/Afbeelding5.png" title="" >}}

Now paste in the console:

```r
library(flempar)
getmp()
```

To avoid using excessive system resources, stop the container once you are done. Just as running the container, stopping the container can be done via the Docker Desktop app and the terminal. Go to the Docker Desktop app and use the stop button.

{{< figure src="/images/Afbeelding6.png" title="" >}}

In the terminal, type the following command.

```
docker stop flempar/rstudio
```
### Building the image - advanced

It could be that you would like to perform additional analysis using other software or other packages. More advanced users who wish to adapt this Docker container can build a new image, using the `flempar` base image. For instance, adding Python libraries to the image etc.

Building a Docker image takes a few simple steps. First, create a text file called *Dockerfile* in your project directory. In this *Dockerfile*, write the instructions on which base image to use and what commands to run to install the dependencies. This base image is the starting point because it provides a foundation of software and dependencies for our application. When all instructions are written in your *Dockerfile*, open a terminal window and navigate to the project directory. Once in the directory, run the command below.

```
docker build -t 'image name' .
```

Note the '.' at the end of the command. It tells the docker to use the current directory. If the instructions and commands are specified correctly, you have now built a Docker Image that can be used to create containers.

### Troubleshooting

```
ERROR: Error response from daemon: open \\.\pipe\docker_engine_windows: The system cannot find the file specified.)
```

Solution: open Powershell (type in menu) and right click, run as administrator. Paste the following command:

```
Enable-WindowsOptionalFeature -Online -FeatureName $(“Microsoft-Hyper-V”, “Containers”) -All
```

