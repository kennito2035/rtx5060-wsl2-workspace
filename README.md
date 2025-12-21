# Python3 Machine Learning Workspace for NVIDIA RTX 5000 series GPUs

This repository contains configuration for a **GPU-accelerated machine learning environment** using Docker and Jupyter Lab for **Windows 11**, specifically optimized for **NVIDIA RTX 5000 series GPUs** for AI training and inference.

**Python version:** 3.12.3

---

## Development System Specs

This workspace was developed and tested on:

- **OS:** Microsoft Windows 11 Home
- **CPU:** Intel® Core™ i5-13450HX
- **GPU:** NVIDIA® GeForce RTX 5060
- **Storage:** 512 GB SSD
- **Memory:** 16 GB RAM
- **WSL:** Ubuntu 24.04

---

## What's Included

- `Dockerfile`: Defines the container image with TensorFlow + CUDA support
- `docker-compose.yml`: Defines the service for local development
- `rtx-5060_dev-requirements.txt`: Python dependencies for ML workflows

---

## Why WSL2 Ubuntu + NVIDIA TensorFlow CUDA Image

TensorFlow no longer supports **native GPU acceleration on Windows after version 2.10**, so you cannot reliably use CUDA/TensorFlow GPU features directly on Windows.

To train models with GPU support (e.g., on an RTX 5060), you need a Linux environment such as **WSL2 Ubuntu**, where Docker + NVIDIA drivers can expose the GPU to containers.

For this reason:
- The base image `nvcr.io/nvidia/tensorflow:25.02-tf2-py3` is an **official NVIDIA container image** that includes pre-installed TensorFlow + CUDA + cuDNN and all the correct GPU libraries
- This ensures optimal compatibility and saves you from manually configuring CUDA, cuDNN, and TensorFlow

---

## Prerequisites

Before running this workspace, ensure you have completed the following:

### Required Setup
- **NVIDIA Driver** (latest version for RTX 5000 series)
- **WSL2** with **Ubuntu 24.04** installed
- Latest **Docker Desktop**
- **CUDA Toolkit** installed on WSL2

### Verification Steps

1. **Verify NVIDIA driver and CUDA are working in WSL2:**
   ```bash
   nvidia-smi
   ```
   This should display your GPU information and CUDA version.

2. **Verify CUDA Toolkit installation:**
   ```bash
   nvcc --version
   ```
   This should display the CUDA compiler version.

### Setup Tutorial

If you haven't set up the above requirements, follow this comprehensive tutorial:

**[WSL2 + NVIDIA Driver + CUDA Toolkit](https://youtu.be/PevhQHn-6R8?si=50_s43qlGhjAzCqC)**

This video covers:
- Installing and updating NVIDIA drivers
- Setting up WSL2 Ubuntu
- Installing CUDA Toolkit in WSL2
- Verifying GPU access in Docker

---

## Build and Run

### 1. Build the container (make sure you're in subdirectory: dockerfiles)

```bash
docker compose build
```

### 2. Start the service

```bash
docker compose up -d
```

### 3. Access Jupyter Lab

Open your browser at:

```
http://127.0.0.1:8888/lab?token=
```

**Token:** `rtx-5060_dev`

### 4. Shutdown the container

To stop and remove the container:

```
docker compose down
```

---

## Project Structure

```
python3-workspace/
├── dockerfiles/
│   ├── Dockerfile
│   ├── rtx-5060_dev-requirements.txt
│   └── docker-compose.yml
└── README.md
```

### File Descriptions

**Dockerfile:**
- Uses NVIDIA TensorFlow + CUDA base image (`nvcr.io/nvidia/tensorflow:25.02-tf2-py3`)
- Installs system dependencies (python3-dev, git, graphviz, OpenGL libraries)
- Copies and installs Python ML dependencies from `rtx-5060_dev-requirements.txt`
- Sets TensorFlow optimization environment variables
- Exposes port 8888 for Jupyter Lab

**docker-compose.yml:**
- Defines the `tf-lab` service with GPU access enabled
- Builds from the Dockerfile in the `dockerfiles/` directory
- Maps the parent directory (`python3-workspace/`) to `/workspace` inside the container
- Configures Jupyter Lab to run on `0.0.0.0:8888` with token authentication
- Creates a persistent volume for Jupyter settings
- Sets resource limits for optimal GPU performance (IPC host mode, memory lock)

**rtx-5060_dev-requirements.txt:**
- Contains Python package dependencies for ML workflows:
  - `numpy<2` - Numerical computing (version constraint for compatibility)
  - `torch`, `torchvision`, `torchaudio` - PyTorch deep learning framework
  - `pandas` - Data manipulation and analysis
  - `scikit-learn` - Machine learning algorithms
  - `matplotlib`, `seaborn` - Data visualization
  - `opencv-python` - Computer vision library
  - `pydot` - Graph visualization
  - `jupyterlab`, `ipykernel` - Interactive notebook environment

---

## Important Notes

- This Docker container is **dedicated for RTX 5000 series GPUs** for AI training and inference in WSL2
- TensorFlow GPU support inside Docker only requires the NVIDIA driver on the host; the CUDA Toolkit itself is provided inside the container image
- NVIDIA's container images from `catalog.ngc.nvidia.com/orgs/nvidia/containers/tensorflow` include CUDA, cuDNN, and TensorFlow correctly configured to work together, reducing setup and compatibility issues
- The container exposes Jupyter Lab on port 8888
- Python 3.12.3 is used inside the container
- Always ensure `nvidia-smi` works properly in WSL2 before running the Docker container