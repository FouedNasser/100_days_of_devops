# Day 47: Docker Python App

## Challenge Description
A Python app needs to be Dockerized and then deployed on App Server 1 (`stapp01`). We have already copied a `requirements.txt` file (having the app dependencies) under `/python_app/src/` directory on App Server 1. We need to:

1.  Create a Dockerfile under `/python_app` directory:
    *   Use any python image as the base image.
    *   Install the dependencies using `requirements.txt` file.
    *   Expose the port `8085`.
    *   Run the `server.py` script using `CMD`.
2.  Build an image named `nautilus/python-app` using this Dockerfile.
3.  Create a container named `pythonapp_nautilus` from the built image:
    *   Map port `8085` of the container to the host port `8096`.
4.  Test the app using `curl` on App Server 1.

## Key Concepts
*   **Dockerfile:** A text document that contains all the commands a user could call on the command line to assemble an image.
*   **`FROM`:** Initializes a new build stage and sets the Base Image.
*   **`WORKDIR`:** Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions.
*   **`COPY`:** Copies new files or directories from `<src>` and adds them to the filesystem of the container at the path `<dest>`.
*   **`RUN`:** Executes any commands in a new layer on top of the current image and commits the results.
*   **`EXPOSE`:** Informs Docker that the container listens on the specified network ports at runtime.
*   **`CMD`:** Provides defaults for an executing container.
*   **`docker build`:** Command to build an image from a Dockerfile.
*   **`docker run`:** Command to run a command in a new container.

## Solution

1.  **Login to App Server 1:**
    ```bash
    ssh tony@stapp01
    ```

2.  **Navigate to the app directory:**
    ```bash
    cd /python_app
    ```

3.  **Create the Dockerfile:**
    We created a file named `Dockerfile` with the following content:

    ```dockerfile
    FROM python:3.6

    WORKDIR /app

    COPY src/requirements.txt /app/
    RUN pip install -r requirements.txt

    COPY src/server.py /app/

    EXPOSE 8085

    CMD ["python", "server.py"]
    ```

    *Note: We assumed `server.py` is in the `src` directory as well, based on the `requirements.txt` location.*

4.  **Build the Docker image:**
    We built the image with the tag `nautilus/python-app`:
    ```bash
    sudo docker build -t nautilus/python-app .
    ```

5.  **Run the container:**
    We started the container named `pythonapp_nautilus` mapping host port 8096 to container port 8085:
    ```bash
    sudo docker run -d -p 8096:8085 --name pythonapp_nautilus nautilus/python-app
    ```

## Verification
To verify the deployment, we checked the running containers and tested the application response.

1.  **Check running containers:**
    ```bash
    sudo docker ps
    ```
    We verified that `pythonapp_nautilus` is running.

2.  **Test the application:**
    We used `curl` to access the application on the mapped host port:
    ```bash
    curl http://localhost:8096
    ```
    This should return the expected output from the Python application.
