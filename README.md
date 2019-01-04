# TorchGAN dockerfiles

## Specs

You get the following out of the box on using these docker images

* Ubuntu
* CUDA 9 (**GPU Version Only**)
* CUDNN 7 (**GPU Version Only**)
* Pytorch 1.0
* Torchvision
* Torchgan
* TensorFlow (for Logging Purposes)
* TensorBoardX
* Visdom
* A few other libraries like numpy

## Setup
### Prerequisites
1. Install Docker following the installation guide for your platform: [https://docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/)

2. **GPU Version Only**: Install Nvidia drivers on your machine either from [Nvidia](http://www.nvidia.com/Download/index.aspx?lang=en-us) directly or follow the instructions [here](https://github.com/saiprashanths/dl-setup#nvidia-drivers). Note that you _don't_ have to install CUDA or cuDNN. These are included in the Docker container.

3. **GPU Version Only**: Install nvidia-docker: [https://github.com/NVIDIA/nvidia-docker](https://github.com/NVIDIA/nvidia-docker), following the instructions [here](https://github.com/NVIDIA/nvidia-docker/wiki/Installation). This will install a replacement for the docker CLI. It takes care of setting up the Nvidia host driver environment inside the Docker containers and a few other things.

### Obtaining the Docker image
You have 2 options to obtain the Docker image

#### Option 1: Download the Docker image from Docker Hub
Docker Hub is a cloud based repository of pre-built images. You can download the image directly from here. This is much faster compared to building the images locally. The image is built based on the `Dockerfile` in the Github repo.

**CPU Version**
```bash
docker pull avikpal/torchgan:cpu
```

**GPU Version**
```bash
docker pull avikpal/torchgan:gpu
```

#### Option 2: Build the Docker image locally
Alternatively, you can build the images locally. Note, this will take a long time/

```bash
git clone https://github.com/torchgan/dockerfiles
cd dockerfiles
```

**CPU Version**
```bash
docker build -t torchgan/torchgan:cpu -f Dockerfile.cpu .
```

**GPU Version**
```bash
docker build -t torchgan/torchgan:gpu -f Dockerfile.gpu .
```
This will build a Docker image named `dl-docker` and tagged either `cpu` or `gpu` depending on the tag your specify. Also note that the appropriate `Dockerfile.<architecture>` has to be used.

## Running the Docker image as a Container
Once we've built the image, we have all the frameworks we need installed in it. We can now spin up one or more containers using this image, and you should be ready to [go deeper](http://imgur.com/gallery/BvuWRxq)

**CPU Version**
```bash
docker run -it -p 8888:8888 -p 6006:6006 -p 8097:8097 -v /sharedfolder:/root/sharedfolder torchgan/torchgan:cpu bash
```

**GPU Version**
```bash
nvidia-docker run -it -p 8888:8888 -p 6006:6006 -p 8097:8097 -v /sharedfolder:/root/sharedfolder torchgan/torchgan:gpu bash
```
Note the use of `nvidia-docker` rather than just `docker`

| Parameter      | Explanation |
|----------------|-------------|
|`-it`             | This creates an interactive terminal you can use to iteract with your container |
|`-p 8888:8888 -p 6006:6006 -p 8097:8097`    | This exposes the ports inside the container so they can be accessed from the host. The format is `-p <host-port>:<container-port>`. The default iPython Notebook runs on port 8888, Tensorboard on 6006 and Visdom on 8097 |
|`-v /sharedfolder:/root/sharedfolder/` | This shares the folder `/sharedfolder` on your host machine to `/root/sharedfolder/` inside your container. Any data written to this folder by the container will be persistent. You can modify this to anything of the format `-v /local/shared/folder:/shared/folder/in/container/`. See [Docker container persistence](#docker-container-persistence)
|`torchgan/torchgan:cpu`   | This the image that you want to run. The format is `image:tag`. In our case, we use the image `torchgan` and tag `gpu` or `cpu` to spin up the appropriate image |
|`bash`       | This provides the default command when the container is started. Even if this was not provided, bash is the default command and just starts a Bash session. You can modify this to be whatever you'd like to be executed when your container starts. For example, you can execute `docker run -it -p 8888:8888 -p 6006:6006 torchgan/torchgan:cpu jupyter notebook --allow-root`. This will execute the command `jupyter notebook` and starts your Jupyter Notebook for you when the container starts

**NOTE** Visdom logging is disabled by default. If you choose to use it please refer to the docs [here](https://torchgan.readthedocs.io/en/stable/modules/logging.html#backends)

## Credits
Parts of this README and Dockerfiles have been borrowed from https://github.com/floydhub/dl-docker.
