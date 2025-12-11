# Day 11: Install and Configure Tomcat Server

## Key Concepts

*   **Tomcat Server:** Apache Tomcat is an open-source implementation of the Java Servlet, JavaServer Pages, Java Expression Language, and Java WebSocket technologies. It provides a "pure Java" HTTP web server environment in which Java code can run.
*   **WAR Files:** A WAR (Web Application Archive) file is a file used to distribute a collection of JAR-files, JavaServer Pages, Java Servlets, Java classes, XML files, tag libraries, static web pages (HTML and related files) and other resources that together constitute a web application.
*   **Service Configuration:** Modifying configuration files (like `server.xml` for Tomcat) to change default behaviors, such as the listening port.
*   **SCP (Secure Copy Protocol):** Used to securely transfer files between hosts on a network.

## Solution

The goal is to install the Tomcat server on App Server 3, configure it to run on port 6300, and deploy the `ROOT.war` application.

### 1. SSH into App Server 3

First, we log in to App Server 3 from the Jump Host.

```bash
ssh banner@stapp03
```
*(Password: BigGr33n)*

### 2. Install Tomcat

We install the Tomcat package using `yum` (since the environment is CentOS-based).

```bash
sudo yum install -y tomcat
```

### 3. Change Tomcat Port to 6300

We need to modify the Tomcat configuration to listen on port 6300 instead of the default 8080. We edit the `server.xml` file.

```bash
sudo vi /etc/tomcat/server.xml
```

We find the Connector block and change the port:

**Original:**
```xml
<Connector port="8080" protocol="HTTP/1.1" ...
```

**Modified:**
```xml
<Connector port="6300" protocol="HTTP/1.1" ...
```

Save and exit the file.

### 4. Start and Enable Tomcat

We verify the configuration change and start the service.

```bash
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
```

We can check the status to ensure it is running:
```bash
sudo systemctl status tomcat
```

### 5. Deploy ROOT.war

The `ROOT.war` file is initially located on the Jump Host at `/tmp/ROOT.war`. We need to transfer it to App Server 3 and then place it in the Tomcat `webapps` directory.

**On Jump Host:**
We copy the file to the `/tmp` directory of App Server 3.

```bash
scp /tmp/ROOT.war banner@stapp03:/tmp/
```

**On App Server 3:**
We move the file to the Tomcat `webapps` directory.

```bash
sudo cp /tmp/ROOT.war /usr/share/tomcat/webapps/ROOT.war
```

Then, we restart the Tomcat service to deploy the application.

```bash
sudo systemctl restart tomcat
```

## Verification

To verify that the application is deployed and accessible on the correct port, we can use `curl` from App Server 3 (or the Jump Host).

```bash
curl http://stapp03:6300
```

This request should return the HTML content of the deployed application homepage.
