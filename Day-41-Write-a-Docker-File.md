# Day 41: Write a Docker File

## Challenge Description
The Nautilus application development team required a custom Docker image for one of their projects. Our task was to create a Dockerfile at `/opt/docker/Dockerfile` (with a capital 'D') on App Server 1 (`stapp01`). This Dockerfile needed to be configured to build an image using `ubuntu:24.04` as the base, install `apache2`, and specifically configure Apache to listen on port `8084`, without altering any other default Apache configuration settings like the document root.

## Key Concepts
*   **Dockerfile:** A script that contains a series of commands and instructions used by Docker to automatically build a new Docker image.
*   **`FROM` Instruction:** Specifies the base image upon which the new image will be built. `ubuntu:24.04` provides a minimal Ubuntu environment.
*   **`ENV DEBIAN_FRONTEND=noninteractive`:** Sets an environment variable to prevent `apt-get` commands from prompting for user input during installation, crucial for automated builds.
*   **`RUN` Instruction:** Executes commands in a new layer on top of the current image, installing packages (`apache2`) and modifying configuration files (`ports.conf`).
*   **`sed` Command:** A stream editor used here to replace the default `Listen 80` directive in Apache's `ports.conf` with `Listen 8084`.
*   **`apachectl configtest`:** Command to verify Apache configuration syntax, ensuring no errors before starting the service.
*   **`EXPOSE` Instruction:** Informs Docker that the container will listen on the specified network port (8084) at runtime. This is primarily for documentation and network configuration.
*   **`CMD` Instruction:** Provides default commands to execute when a container is launched from the image. `apachectl -D FOREGROUND` starts Apache in the foreground, ensuring the container remains active as long as Apache is running.

## Solution

We followed these steps to create the Dockerfile on `stapp01`:

1.  **SSH into Application Server 1 (`stapp01`):**
    We connected to `stapp01` using the `tony` user.
    ```bash
    ssh tony@stapp01
    # Password: Ir0nM@n
    ```

2.  **Create the directory for the Dockerfile:**
    ```bash
    sudo mkdir -p /opt/docker
    ```

3.  **Create the Dockerfile with the specified content:**
    We used `sudo vi /opt/docker/Dockerfile` and added the following exact content:
    ```dockerfile
    FROM ubuntu:24.04

    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update && \
        apt-get install -y apache2 && \
        sed -i 's/^Listen 80$/Listen 8084/' /etc/apache2/ports.conf && \
        apachectl configtest

    EXPOSE 8084

    CMD ["apachectl", "-D", "FOREGROUND"]
    ```

## Verification
To verify this task, we would typically build the Docker image and run a container from it:

1.  **Build the Docker image:**
    ```bash
    sudo docker build -t my-apache-image:latest /opt/docker
    ```

2.  **Run a container from the image, mapping port 8084:**
    ```bash
    sudo docker run -d -p 8084:8084 --name my-apache-container my-apache-image:latest
    ```

3.  **Check if Apache is serving on port 8084:**
    ```bash
    curl http://localhost:8084
    ```
    We would expect to receive the default Apache welcome page, confirming the port change.

