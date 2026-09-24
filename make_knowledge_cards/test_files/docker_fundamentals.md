# Docker and Container Fundamentals

## What is a Container

A container is a lightweight, standalone executable package that includes everything needed to run a piece of software, including the code, runtime, system tools, system libraries, and settings. Containers isolate applications from each other and from the underlying infrastructure, ensuring consistent behavior across different environments.

Unlike virtual machines, containers share the host operating system's kernel rather than running a full guest OS. This makes containers much smaller in size, typically tens of megabytes compared to gigabytes for virtual machines. A container starts in seconds rather than minutes.

## Container Images

A container image is a read-only template with instructions for creating a Docker container. Images are built from a Dockerfile, which is a text file containing a series of instructions. Each instruction adds a new layer to the image.

Images are stored in registries. Docker Hub is the default public registry, but organizations can run private registries for internal use. An image is referenced by a name and tag, such as `nginx:1.21`. If no tag is specified, Docker uses `latest` by default.

## Layered Filesystem

Docker images use a layered filesystem based on Union File System. Each instruction in a Dockerfile creates a new layer. Layers are stacked on top of each other and shared across images. When a container is started, a thin writable layer is added on top of the read-only image layers.

This layered approach means that if two images share the same base layers, they share the same files on disk. This saves storage space and reduces build time. When a container modifies a file, Docker uses copy-on-write: the file is copied from the read-only layer to the writable layer, and the modification is made there. The original file in the read-only layer remains unchanged.

## Docker Architecture

Docker uses a client-server architecture. The Docker client (docker CLI) communicates with the Docker daemon (dockerd) via a REST API. The daemon is responsible for building, running, and managing containers.

The daemon runs on the host operating system and interacts with the kernel to manage container processes. Docker client can connect to a local or remote daemon. The communication happens over Unix sockets or TCP.

## Container Networking

Docker provides several network drivers for container communication. The bridge network is the default, which creates an internal network on the host. Containers on the same bridge network can communicate with each other using IP addresses.

The host network driver removes network isolation between the container and the host, using the host's networking directly. The none network driver disables all networking for the container. For multi-host communication, Docker supports overlay networks, which use VXLAN to create a virtual network spanning multiple Docker hosts.

Port mapping allows exposing container ports to the host. For example, `docker run -p 8080:80 nginx` maps port 80 in the container to port 8080 on the host. This is essential for making containerized applications accessible from outside the host.

## Data Volumes and Persistence

Containers are ephemeral by design. When a container is removed, its writable layer is also removed, and any data stored in it is lost. Docker volumes provide a mechanism for persisting data beyond the container lifecycle.

A volume is a directory on the host that is mounted into the container. Volumes are managed by Docker and are independent of the container's lifecycle. They can be shared between containers and are stored in a specific location on the host. Bind mounts are an alternative that allows mounting any directory on the host into the container, but they are less portable than volumes.

## Docker Compose

Docker Compose is a tool for defining and running multi-container applications. It uses a YAML file (docker-compose.yml) to configure all services, networks, and volumes needed for an application. A single command (`docker compose up`) starts the entire application stack.

Compose is particularly useful for development and testing environments. For example, a web application might need a web server, an application server, and a database running together. With Compose, all three services can be defined, configured, and started together, with proper networking and dependency management.

## Container Orchestration

As the number of containers grows, managing them manually becomes impractical. Container orchestration tools automate the deployment, scaling, and management of containerized applications. Kubernetes is the most popular orchestration platform.

Orchestration tools handle scheduling (deciding which host runs which container), scaling (adding or removing instances based on load), self-healing (restarting failed containers), and rolling updates (deploying new versions without downtime). They also manage service discovery, load balancing, and configuration management across a cluster of machines.
