############################################
# 1.) Base: NVIDIA TensorFlow + CUDA image
############################################
FROM nvcr.io/nvidia/tensorflow:25.02-tf2-py3

############################################
# 2.) Working directory
############################################
WORKDIR /workspace

############################################
# 3.) System-level dependencies
############################################
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3-dev \
    git \
    graphviz \
    libgl1 \
    libglib2.0-0 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
	
############################################
# 4.) Copy requirements for caching
############################################
COPY dockerfiles/rtx-5060_dev-requirements.txt .

############################################
# 5.) Python dependencies (TensorFlow already part of base)
############################################
RUN pip install --no-cache-dir -r rtx-5060_dev-requirements.txt

############################################
# 6.) Optimize environment
############################################
ENV TF_ENABLE_ONEDNN_OPTS=0
ENV CUDA_MODULE_LOADING=LAZY

############################################
# 7.) Ports + user
############################################
EXPOSE 8888

############################################
# Default entry
############################################
CMD ["bash"]