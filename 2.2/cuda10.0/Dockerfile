FROM nvidia/cuda:10.0-runtime-ubuntu18.04

# TensorFlow version
ENV TF_VERSION_MAJOR=2
ENV TF_VERSION_MINOR=2
ENV TF_VERSION=$TF_VERSION_MAJOR.$TF_VERSION_MINOR

# CUDA version (needs to match the docker image specified above)
ENV CUDA_VERSION_MAJOR=10
ENV CUDA_VERSION_MINOR=0
ENV CUDA_VERSION=$CUDA_VERSION_MAJOR.$CUDA_VERSION_MINOR

# CUDNN version
ENV CUDNN_VERSION_MAJOR=7
ENV CUDNN_VERSION_MINOR=6.5.32
ENV CUDNN_VERSION=$CUDNN_VERSION_MAJOR.$CUDNN_VERSION_MINOR

# Python version (only works with python 3+)
ENV PYTHON_VERSION_MAJOR=3
ENV PYTHON_VERSION_MINOR=6
ENV PYTHON_VERSION=$PYTHON_VERSION_MAJOR.$PYTHON_VERSION_MINOR

# Bazel version
ENV BAZEL_VERSION_MAJOR=2
ENV BAZEL_VERSION_MINOR=0.0
ENV BAZEL_VERSION=$BAZEL_VERSION_MAJOR.$BAZEL_VERSION_MINOR

# Install all the packages needed
RUN apt-get update && apt-get install -y --no-install-recommends \
  cuda-nvml-dev-$CUDA_PKG_VERSION \
  cuda-command-line-tools-$CUDA_PKG_VERSION \
  cuda-libraries-dev-$CUDA_PKG_VERSION \
  cuda-minimal-build-$CUDA_PKG_VERSION \
  libnccl-dev=$NCCL_VERSION-1+cuda$CUDA_VERSION \
  libcudnn$CUDNN_VERSION_MAJOR=$CUDNN_VERSION-1+cuda$CUDA_VERSION \
  libcudnn$CUDNN_VERSION_MAJOR-dev=$CUDNN_VERSION-1+cuda$CUDA_VERSION \
  wget \
  git \
  build-essential \
  python$PYTHON_VERSION_MAJOR \
  python$PYTHON_VERSION_MAJOR-dev \
  python$PYTHON_VERSION_MAJOR-pip \
&& \
  apt-get -y autoremove \
&& \
  rm -rf /var/lib/apt/lists/* \
&& \
  ln -s /usr/bin/python$PYTHON_VERSION /usr/bin/python \
&& \
  wget --no-verbose -O /usr/bin/bazel https://github.com/bazelbuild/bazel/releases/download/$BAZEL_VERSION/bazel-$BAZEL_VERSION-linux-x86_64 \
&& \
  chmod 555 /usr/bin/bazel \
&& \
  pip$PYTHON_VERSION_MAJOR install pip --upgrade \
&& \
  hash -r pip$PYTHON_VERSION_MAJOR \
&& \
  pip$PYTHON_VERSION_MAJOR install six numpy wheel mock setuptools keras_applications keras_preprocessing

ENV LIBRARY_PATH /usr/local/cuda/lib64/stubs:/usr/local/cuda/extras/CUPTI/lib64:$LD_LIBRARY_PATH

# Compile TensorFlow from source
WORKDIR /tensorflow
RUN git clone --depth 1 --branch=r$TF_VERSION https://github.com/tensorflow/tensorflow.git .
ENV TF_NEED_GCP=1
ENV TF_NEED_HDFS=1
ENV TF_NEED_S3=1
ENV TF_NEED_CUDA=1
ENV TF_CUDA_VERSION=$CUDA_VERSION_MAJOR
ENV TF_CUDNN_VERSION=$CUDNN_VERSION_MAJOR
ENV TF_CUDA_COMPUTE_CAPABILITIES=3.5,3.7,5.2,6.0,6.1,7.0
ENV TF_NEED_TENSORRT=0
ENV CC_OPT_FLAGS='-mavx'
RUN yes "" | python$PYTHON_VERSION configure.py
RUN bazel build --config=opt --config=cuda --config=v2 tensorflow/tools/pip_package:build_pip_package
RUN bazel-bin/tensorflow/tools/pip_package/build_pip_package .pip_package
