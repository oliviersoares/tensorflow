This repo provides docker containers to compile your own TensorFlow from source against different CUDA versions.

For e.g. TensorFlow 2.1 is compiled by default against CUDA 10.1 (binaries provided by Google via pip). You might for some reason need a version compiled against CUDA 10.0 instead of 10.1.
Or you might just want to compile it with your flags or set of options. This tutorial should help you get started.

I'll assume that you're using Ubuntu (18.04 LTS for example) tho you should be able to do this from any computer where you can install Docker and have an NVIDIA graphics card with proper drivers.

Make sure that you have at least NVIDIA driver 418 installed. You don't need to install CUDA. CUDA will come with the container.

To install the NVIDIA drivers on Ubuntu, you can for example run this:
```sh
NVIDIA_VERSION=418
sudo add-apt-repository -y ppa:graphics-drivers/ppa
sudo apt-get -y update
sudo apt-get -y install nvidia-driver-${NVIDIA_VERSION}
```

Now you can install docker. Just follow these instructions:
https://docs.docker.com/install/linux/docker-ce/ubuntu/

Once installed you can either build TensorFlow from source against CUDA 10.0, CUDA 10.1 or CUDA 10.2.

To compile TensorFlow 2.2 against CUDA 10.0, just run the following command:
```sh
docker build -t tensorflow_2.2_cuda10.0 2.2/cuda10.0
```

Depending on your machine, it can take several hours to compile. Check if there's any error once it's done.

You can now copy the .whl package from the docker container to your host to install it with pip.

First start the container:
```sh
docker run -it tensorflow_2.2_cuda10.0
```

Then look at the running containers:
```sh
docker ps
```

You should see one line (unless you have other containers running) with the name tensorflow_2.2_cuda10.0. The first row is the container ID. Remember it.

Now you can copy the .whl from the container to the host with this command (replace the container ID):
```sh
docker cp <CONTAINER_ID>:/tensorflow/.pip_package/tensorflow-2.2.0-cp36-cp36m-linux_x86_64.whl ~/tensorflow-2.2.0-cp36-cp36m-linux_x86_64.whl
```

Now you should be able to distribute this .whl or install it with pip:
```sh
pip install --upgrade ~/tensorflow-2.1.0-cp36-cp36m-linux_x86_64.whl
```

To do the same thing against CUDA 10.1, just run this command instead:
```sh
docker build -t tensorflow_2.2_cuda10.1 2.2/cuda10.1
```

And follow the same steps as described before, just replacing "10.0" with "10.1".
