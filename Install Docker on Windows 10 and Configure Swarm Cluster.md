# Install Docker on Windows 10 and Configure Swarm Cluster
**Docker** is an open-source software platform designed to build, deploy and execute applications more efficiently. Docker allows to separate applications from the underlying infrastructure to deploy 
applications quickly without the need of worrying if applications work on the deployed environment. Using Dockers methodologies for shipping, testing and deploying code, developers can significantly reduce the 
delay between writing the code and running it in production.

Docker is programmed in **Go** programming language. It was first released in **2013 by Docker, Inc** to work on **Linux** platform and later, it was extended to offer support on other platforms including 
**Microsoft Windows** and **Apple MacOS**. 

Docker uses **containerization** technology using which one can develop and package the application along with all its dependencies into a standardized unit called **Container**. Docker containers are light-
weight, portable and isolated from the underlying infrastructure so that they can be easily shipped and run consistently on different platforms such as Linux, Windows and MacOS and across different locations 
including on-premises, public cloud and private cloud. 

Docker is not a or does not work like a Virtual Machine:  
* **Virtual Machine** is a system like a computer having a fully copy of operating system, application, binaries, libraries etc. Virtual Machine uses **hypervisor** technology which allows VM to run a guest operating 
system with virtual access to host operating system resources. VMs are heavy in nature since they have full OS with its own memory management with the overhead of virtual device drivers. 
* **Container** is not a VM since it does not require a separate OS and is deployed on a physical machine with a host OS. Container consists of everything needed including application, binaries, libraries 
for application to work but it depends on the underlying **machine OS kernel** to run.

The key components of Docker include **Docker Host**, **Docker Engine** which comprises of _**Docker Daemon**, **Docker API**, **Docker Client**_, Docker Objects which include _**Dockerfile**, **Docker images**, 
**Docker Containers**, **Docker Storage** and **Docker Network**_, **Docker Registry**, **Docker Desktop**, **Docker Compose** and **Docker Swarm**.

Due to Docker’s popularity, many cloud platforms such as **AWS** and **Azure** support Docker containers to package, deploy and run applications.

This document provides instructions to install Docker and configure Docker Swarm Cluster.
You can also go through [this PDF document](/doc/Install%20Docker%20on%20Windows%2010.pdf) for installation steps along with screenshots.  
<br/>

## 1. Install Docker on Windows:
On a Linux host, Docker is installed directly on the operating system but on Windows, Docker is installed inside a Linux VM _(created using either Hyper-V or WSL2)_ and Docker client is used to interact with 
Docker Engine.

Installing Docker on Windows will actually install the following components inside a Linux VM on Windows system:
* Docker Server
* Docker Engine – Docker daemon, dockerd
* ContainerD — Container runtime, containerd
* RunC — Low level container runtime, runc
* docker-init
* Docker Client – Docker CLI, docker

### 1.1. Install Docker:
Follow the below steps to install Docker in Windows 10 system:
* Go to [Docker Desktop Windows Install](https://docs.docker.com/desktop/setup/install/windows-install/) page and click on the **Docker Desktop for Windows - x86_64** button which downloads
  `Docker Desktop Installer.exe` file into your **Downloads** folder.  

  **Note:** By default, `Docker Desktop Installer.exe` file installs at `C:\Program Files\Docker\Docker` location and this location cannot be changed from the GUI. As an alternate, install it from the command line
  by passing the installation directory and using the default WSL2 backend for Docker Desktop.
* Open **Command Prompt** application in **Administrator** mode and run the following commands:
  ```
  cd %USERPROFILE%\Downloads
  start /w "" "Docker Desktop Installer.exe" install --accept-license --installation-dir="D:\ProgramFiles\Docker" --backend=wsl-2 --wsl-default-data-root="D:\ProgramData\Docker\wsl" --windows-containers-default-data-root="D:\ProgramData\Docker\containers" --hyper-v-default-data-root="D:\ProgramData\Docker\vm"
  ```

  To run from **Windows PowerShell** application in **Administrator** mode, use the following commands for Docker installation:
  ```
  cd $env:USERPROFILE\Downloads
  Start-Process 'Docker Desktop Installer.exe' -Wait 'install --accept-license --installation-dir="D:\ProgramFiles\Docker" --backend=wsl-2 --wsl-default-data-root="D:\ProgramData\Docker\wsl" --windows-containers-default-data-root="D:\ProgramData\Docker\containers" --hyper-v-default-data-root="D:\ProgramData\Docker\vm"'
  ```
  In the above commands, the installation directory is specified as `D:\ProgramFiles\Docker` to install at this location and backend as `wsl-2` to use _(even if `--backend` flag is not specified,
  it uses `wsl-2` by default but it also allows to specify `hyper-v` or `windows` values to use a different backend)_.
* The installer then starts extracting and checks prerequisites which opens the installer wizard.
* Once all prerequisites are validated, it shows the **Configuration** page where select the **Add shortcut to desktop** checkbox and press **OK** button.
* Then, it starts with the installation by unpacking files and takes few minutes to complete.
* It displays **Installation succeeded** message once done.
* Click on **Close** button to exit the installer.
* On the **Command Prompt** also, it displays **Installation Succeeded** message.
  
### 1.2. Start Docker Desktop:
After Docker installation, Docker Engine does not start automatically. **Docker Desktop** application needs to be started which automatically starts the Docker Engine.

* Search for **Docker** in the search box of the task bar and select **Docker Desktop** application.
* In few seconds, it might launch **Windows Subsystem for Linux** (WSL) app since Docker uses this at the backend. This can be closed.
* In few seconds, it launches **Docker Desktop** application with a **Welcome** page.
* At the top of Docker Destkop, you can see the Docker Subscription Service Agreement which is **PERSONAL**. Click on **Skip** link to skip it for now.
* It then displays a **Welcome Survey** page. Click on **Skip** link to skip this survey for now.
* After that, it displays Docker Desktop Dashboard with various tabs such as **Containers**, **Images**, **Volumes**, **Builds**, **Docker Hub**, etc. At the bottom, you can see that **Engine running** with
  **RAM**, **CPU** and **Disk storage** being used.
* Currently, there is no container running due to which Docker engine goes into **Resource Save mode** status in few seconds. It comes into running state again when a container
  is started.

### 1.3. Verify Docker:
Run the following command to get the installed Docker version:
```
docker --version
```

Use the below command to verify the version of all Docker components
```
docker version
```
Now that Docker has installed successfully, it is important to get hands on Docker commands to work with Docker. Follow [this link](https://github.com/srimarrivada/Docker-Commands/blob/main/README.md) to 
understand the basic Docker commands.
<br/>
<br/>

## 2. Docker Image from Dockerfile:
A Docker image can be created from a `Dockerfile` starting with the base image available in the Docker registry and the resulting image is structured in the form of layers. 
Refer [here](https://github.com/srimarrivada/Dockerfile-Instructions/blob/main/README.md) to know more about `Dockerfile` instructions.

### 2.1. Create Dockerfile:
Create a file named `Dockerfile` (without any extension) in the desired directory (let's say `D:\Learning\Docker`). By default, Docker searches for the exact file named `Dockerfile` while building it. However, 
`Dockerfile` naming convention is not mandatory and can have a different name which can be provided to Docker while building but for now, let’s go with `Dockerfile` naming.

### 2.2. Add Instructions:
Open `Dockerfile` and add the below instructions for a simple `Ubuntu` image.
```
# Use the official ubuntu image as a base
FROM ubuntu

# Define author of this image
MAINTAINER Docker-User user001@example.com

# Add some metadata for this image
LABEL version="1.0" description="Simple Ubuntu image"

# Install dependencies
RUN apt-get update

# Execute command during container creation
CMD ["echo", "Hello World!"]
```
Save the file after adding instructions.

### 2.3. Build Docker Image:
Open a new **Command Prompt** and run the following command to build the image named `my-ubuntu` and tagged `v1`:
```
docker build -t my-ubuntu:v1 D:\Learning\Docker
```
Note that my `Dockerfile` is present in `D:\Learning\Docker` directory.  
If your `Dockerfile` is present with a different name (say `Dockerfile.txt`) in your current directory, then use the below command _(here `.` represents current directory from wherever this command is executed)_.
```
docker build -t my-ubuntu:v1 . -f Dockerfile.txt
```

Now, run the below command to see if the newly created image is available:
```
docker images
```

### 2.4. Run Docker Image:
Once the image is created, a container will be created by running the image using the below command:
```
docker run my-ubuntu:v1
```
When the container is created, it executes the command that was specified in `Dockerfile` and exits.

Run the below command to see the list of all containers:
```
docker ps -a
```
<br/>

## 3. Docker Desktop:
Docker Desktop is a Graphical User Interface (GUI) provided by Docker to manage (build, run, share) images, containers, networks and volumes visually from the host machine. It allows to integrate various 
development tools and languages that saves development time, to automate builds and to collaborate securely with shared repositories. It includes monitoring tools to track container performance metrics, 
health checks and offers logging capabilities to capture container logs for debugging purposes.

Docker Desktop includes components such as **Docker Engine**, **Docker CLI**, **Docker Build**, **Docker Compose**, **Docker Extensions**, **Docker Content Trust**, **Kubernetes**, **Credentials Helper** and 
**Ask Gordon** (a personal AI assistant).

### 3.1. Launch Docker Desktop:
Open **Docker Desktop** application by searching for **Docker** in the search box of the task bar in your Windows system and selecting the **Docker Desktop** application.  
In few seconds, it launches **Docker Desktop** application and displays the **Docker Dashboard** with various tabs such as **Containers**, **Images**, **Volumes**, **Builds**, **Docker Hub**, **Docker Scout** 
and **Extensions**. 

### 3.2. Containers View:
The **Containers** view in Docker Desktop provides a graphical interface to manage the life cycle of containers including start, stop, pause, resume, restart and delete. It lists all running and stopped 
containers and provides a runtime view of all containers and applications. 

* To start a container, select the container name, _(for example, container with `my-ubtunu` image)_, and click on **Start** icon under **Actions** menu.
* Once the container is started successfully, it changes to **Running** state. Click on **Show Containers Actions** icon under **Actions** menu to view container details, view image packages and CVEs, 
  copy docker run, open integrated terminal, view container files, pause the container and restart the container.
* Click on `mysql` container that is running and select **View details** option under **Show Containers Actions** icon to inspect the container which displays various tabs such as:
  * **Logs** _(equivalent to `docker logs <container-id>` command)_
  * **Inspect** _(equivalent to `docker inspect <container-id>` command)_
  * **Bind** 
  *	**Exec**  _(equivalent to `docker exec -it <container-id> /bin/sh` command)_
  * **Files**
  * **Stats** 

### 3.3. Images View:
The **Images** view in Docker Desktop provides a graphical interface to manage images. It lists all Docker images available locally and shared on Docker Hub. It allows to run an image as a container, pull the 
latest version of an image from Docker Hub, push the image to Docker Hub and inspect images.
* To run an image as a container, select the image, here it is `mysql`, and click on **Run** icon under **Actions** menu.
* When prompted, select the **Optional settings** drop-down and specify the container name (let's say `mysql_2`) and add the **Environment variable** as **MYSQL_ROOT_PASSWORD** and provide some password
  (let’s say `mysql123`) and select **Run**.
* Additionally, you can also specify container ports and volumes if needed.
* It then creates a new container `mysql_2` and displays log entries.
* To inspect an image, click on the `mysql` image to display detailed information about the image such as image history, image ID, image created date, image size, layers making up the image, base images used,
  vulnerabilities found and packages inside the image.
* To pull the latest version of the image from Docker Hub, select the image and click on **Show image actions** icon and select **Pull**.  
  **Note:** Repository must exist on Docker Hub in order to pull the latest version of an image.
* To push the image to Docker Hub, select the image and click on **Show image actions** icon and select **Push to Docker Hub**.  
  **Note:** Docker Hub must have already been logged in to push the image if it contains the correct username/organization in its tag.

The **Images** view also allows to manage and interact with images in Docker Hub repositories:
* Click on **Docker Hub repositories** tab and sign in to Docker Hub account.
* When signed in, it shows a list of images in Docker Hub organizations and repositories that you have access to.
* Select an organization from the drop-down to view a list of repositories for that organization.

### 3.4. Volumes View:
The **Volumes** view in Docker Desktop provides a graphical interface to manage Docker volumes which are used to persist data generated and used by Docker containers. It displays list of volumes and 
allows to easily create, inspect, delete, clone, empty, export, and import Docker volumes, browse files and folders in volumes and see which volumes are being used by containers.
* To create a volume, select **Create** button and specify the volume name (let’s say `mysql_volume`) and click on **Create** in **New Volume** modal.
* To inspect a volume, click on the volume to display detailed information about the volume

### 3.5. Builds View:
The **Builds** view in Docker Desktop provides a graphical interface to manage and monitor image builds from Dockerfiles, offering tools for troubleshooting, and integrating build options to streamline Docker 
image creation. It displays a list of all ongoing and completed builds and allows to inspect build history, monitor active builds, and manage builders.

By default, the Builds view displays Build history to view the history of all completed builds but use the toggle option **Show only my builds** allows to display builds created by the current user with 
access to logs, dependencies, traces and others. This view also displays any active or completed cloud builds by other team members if connected to a cloud builder through Docker Build Cloud. 

* Click on **Import builds** button to import build files with `.dockerbuild` or `.dockerbuild.zip` extension for builds by other people. When a build record is imported, it gives full access to the logs,
  traces, and other data for that build, directly in Docker Desktop.
* Click on **Builder settings** button to manage builders including inspecting the state and configuration of active builders, starting and stopping a builder, deleting the build history, adding or removing
  builders, connecting and disconnecting cloud builders directly from the Docker Desktop settings.
* To inspect a build, click on the build name to display detailed information about the build 

### 3.6. Docker Hub View:
The **Docker Hub** view allows to interact with Docker Hub when signed in from the Docker Desktop directly.

### 3.7. Docker Scout View:
The **Docker Scout** view in Docker Desktop allows to access the Docker Scout service to analyze images and raise security vulnerabilities. Docker Scout compiles an inventory of components, also known as a 
**Software Bill of Materials (SBOM)** which is matched against a continuously updated vulnerability database to pinpoint security weaknesses.  

### 3.8. Docker Extensions View:
The **Extensions** view in Docker Desktop allows to use integrate third-party tools seamlessly in Docker Desktop for application development and deployment of workflows. It allows to explore the list of 
available extensions in Docker Hub or in the Extensions Marketplace within Docker Desktop.

### 3.9.Change Settings:
There are two ways to navigate to Docket Desktop settings:
* Select the **Settings** icon on the top right corner of the Docker Desktop Dashboard.
* Right click on the **Docker** menu on the task bar and select **Change settings** option. 

These settings can also be accessed from the `settings-store.json` file located at `C:\Users\[USERNAME]\AppData\Roaming\Docker\settings-store.json` in Windows or at `~/.docker/desktop/settings-store.json` in 
Linux or at `~/Library/Group\Containers/group.com.docker/settings-store.json` in MacOS.
<br/>
<br/>

## 4. Create Python Docker Application:
Now, let us create a simple **Python Flask** containerized application to run from Docker. **Flask** (`flask` library) is a Python framework used to create a web application. 

### 4.1. Verify Python Version:
To create a Python application, Python software should be running in the system. If Python is not installed yet in your system, install it from [Anaconda Distribution](https://www.anaconda.com/docs/main) which is an 
open source software built for **Python** and **R** programming languages. Refer to [Official Anaconda Installation Guide](https://www.anaconda.com/docs/main) on how to install it.

Open a new **Command Prompt** and verify the installed Python version using the below command:
```
python --version
```
In my system, Python `3.13.0` version has been installed.

### 4.2. Launch VS Code:
To create this Python based application, let us use **Microsoft VS Code** software to write the Python script and execute Docker commands for easy management 
_(it is not necessary to have VS Code but it is good to use)_. Follow [these](https://code.visualstudio.com/docs/setup/windows) steps to install VS Code software if you do not have it already.

Though VS Code can be opened directly, let us use **Git** software to launch VS Code. Install Git software on Windows from the [Git website](https://git-scm.com/downloads/win) if you do not have it.

* In the Windows search bar, start typing **gitbash** and select the first match which opens up **Git Bash** application.
* In the **Git Bash** command prompt, run the following commands to create a new directory for our Dockerized Python application at `D:\Learning\Docker\Projects` _(make sure this path exists in your location
  or use a different location as per your choice)_ and open VS Code.
  ```
  cd "D:\Learning\Docker\Projects"
  mkdir PythonFlask
  cd PythonFlask
  code .
  ```
* It opens **VS Code** application which might prompt you to confirm if you trust the authors of file in which case, click on **Yes, I trust the authors** button.
* In VS Code, go to **Terminal** menu and select **New Terminal**. 
* By default, it uses **PowerShell** terminal, but let us choose **Git Bash** terminal from the dropdown in the **Terminal** tab below.

### 4.3. Create Virtual Environment:
Create a new virtual Python environment to have an isolated workspace for our application and install relevant packages without affecting the actual Python environment available in the system.
In the **Terminal** window, run the following command to create virtual environment with **Python 3.7** version:
```
conda create -p venv python=3.7 -y
```
**Note:** Before executing this command, make sure that you have installed Anaconda distribution and set `Scripts` location of Anaconda installation in your **PATH** environment variable, otherwise you might 
encounter **bash: conda: command not found** error.

Once the Python virtual environment got created successfully, you can see that `venv` directory which contains Python related libraries and files got created under `PYTHONFLASK` project in VS Code.

Now, run the following command to activate the new virtual environment in **Terminal** window:
```
conda activate venv
```
When this command is executed, it might throw error **CondaError: Run 'conda init' before 'conda activate'**.
To fix this issue, source the location of `conda.sh` file from your **Anaconda** installation path and then activate the virtual environment using the following commands _(In my case, Anaconda was installed 
at `D:\ProgramFiles\Anaconda\anaconda3` path but if your Anaconda installation path is different, replace my path with your path)_:
```
source "D:\ProgramFiles\Anaconda\anaconda3\etc\profile.d\conda.sh"
conda activate "D:\Learning\Docker\Projects\PythonFlask\venv"
```

To verify if the virtual environment was activated successfully, check the version of Python:
```	
python --version
```
It displays **Python 3.7.16** version in virtual environment.

### 4.4. Create Python File:
In **VS Code** application, click on **New** File icon next to `PYTHONFLASK` project under **EXPLORER** and name the file as `flask_app.py`.
Enter the following code in `flask_app.py` file and save it.
```
import time
from flask import Flask

app = Flask(__name__)
start = time.time()
		
def elapsed():
    running = time.time() - start
    minutes, seconds = divmod(running, 60)
    hours, minutes = divmod(minutes, 60)
    return "%d:%0.2d:%0.2d" %(hours, minutes, seconds)

@app.route("/")
def hello():
    return "Hello Python! (uptime: %s)" %elapsed()

if __name__ == "__main__":
    app.run(debug=True, host="0.0.0.0", port=8070)
```

To make this Python code working successfully, the `flask` Python library should be installed. For this, create a new file under `PYTHONFLASK` project and name it as `requirements.txt`. This file is used to 
install multiple python libraries at once.

Enter the following line in `requirements.txt` file and save it. 
```
flask	
```
Next, run the following command in the **VS Code Terminal** window to install Python library requirements:
```
pip install -r requirements.txt
```

Now, execute the `flask_app.py` file using the following command:
```
python flask_app.py
```

When it runs successfully, it provides two localhost web URLs `http://127.0.0.1:8070` and `http://<machine_ip>:8070` where the Flask application is running. Open any URL to see the output of our python 
application and it displays the uptime which gets updated upon every refresh of the browser URL.

Press **Ctrl+C** on the VS Code terminal to exit out of Flask application.

### 4.5. Create Docker File:
Now, click on **New File** icon under `PYTHONFLASK` project and name it as `Dockerfile` without any extension _(Docker looks for this file name while building an image, however a different file name can also be 
used and must be specified during Docker Build)_. 

Enter the following instructions in `Dockerfile` file and save it. This the basic Docker code for a simple Python application.
```
FROM python:3-alpine
WORKDIR /flask
COPY . .
RUN pip install -r requirements.txt
ENTRYPOINT ["python", "flask_app.py"]
EXPOSE 8082
```
Note that there is a `venv` sub-folder available in `PYTHONFLASK` project and it is not required to copy this sub-folder to the Docker container. To ignore this folder, create a file named `.dockerignore`, 
write the following line and save it so that Docker ignores all files/folders listed in `.dockerignore` file by default while building image.
```
venv
```

### 4.6. Build Docker Image:
Now, open a new terminal in VS Code and run the following command to create a docker image. In this command, the `.` indicates the current working directory where `Dockerfile` is present.
```
docker build -t pythonflaskdemo:latest .
```
If you have used a different Dockerfile name, then specify it in the below command:
```
docker build -t pythonflaskdemo:latest . -f <docker_file>
```

Go to **Docker Desktop** and see that `pythonfalskdemo` image has been created with latest tag name in **Images** view.  
Click on `pythonfalskdemo` image and it displays image ID, image size and layers created to makeup this image.

### 4.7. Run Docker Image:
Now, run the Docker image using the following command in VS Code terminal. In my case, the image Id is `ca313b320078` as displayed in my Docker Desktop. Replace this value with your image ID as you see in your 
Docker Destop:
```
docker run --name pythonflaskdemoapp ca313b320078
```
Go to **Docker Desktop** and see that a new container called `pythonfalskdemoapp` has been created and running in **Containers** view.

Now, when you hit the URL `http://127.0.0.1:8070/` produced by the docker image after running, it displays site cannot be reached error because the flask application port is not mapped to run outside the 
Docker container.

To overcome this problem, first press **Ctrl + C** to exit out of the previous flask execution and run the following command to create a new container and map the flask application port `8070` to port `8030` 
in local system. 
```
docker run --name pythonflaskdemoapp1 -p 8030:8070 pythonflaskdemo
```
Go to **Docker Desktop** which shows a new container called `pythonflaskdemoapp1` has been created and running on `8030` port locally

Now, hit the URL `http://localhost:8030/` produced by the docker container which displays the uptime and gets changed upon every refresh of the browser URL.
<br/>
<br/>

## 5. Create HTML Docker Application:
Let us create another simple HTML containerized application using **Nginx** web server to run from Docker. **Nginx** is a web server software that also acts as a reverse proxy, load balancer, mail proxy and 
HTTP cache. 

### 5.1. Launch VS Code:
In the **Git Bash** command prompt, run the following commands to create a new directory at `D:\Learning\Docker\Projects` _(make sure this path exists in your location or use a different location as per your 
choice)_ and open VS Code.
```
cd "D:\Learning\Docker\Projects"
mkdir HtmlNginx
cd HtmlNginx
code .
```
It opens **VS Code** application which might prompt you to confirm if you trust the authors of file in which case, click on **Yes, I trust the authors** button.

In **VS Code**, go to **Terminal** menu and select **New Terminal** and choose **Git Bash** terminal from the dropdown in the **Terminal** tab below.

### 5.2. Create HTML File:
In **VS Code** application, click on **New File** icon next to `HTMLNGINX` project under **EXPLORER** and name the file as `index.html`.  
Enter the following code in `index.html` file and save it.
```
<!DOCTYPE html>
<html>
	<head>		
		<title>To-Do List</title>
	</head>
	
	<body>
		<h2>To-Do List</h2>
		<p>Welcome User!! You can add your to-do list here..</p>

		<input type="text" id="todoInput" placeholder="Enter a new task...">
		<button onclick="newElement()">Add</button>

		<ul id="todoList"></ul>

		<script>
			// Function to add new list items
			function newElement() {
				var li = document.createElement("li");
				var inputValue = document.getElementById("todoInput").value;
				li.appendChild(document.createTextNode(inputValue));

				if (inputValue === '') {
				alert("You must enter something!");
				} else {
				document.getElementById("todoList").appendChild(li);
				}
				document.getElementById("todoInput").value = "";
				
				// Add 'x' next to item to delete on click
				var span = document.createElement("SPAN");
				var txt = document.createTextNode("\u00D7");
				span.className = "close";
				span.appendChild(txt);
				li.appendChild(span);

				span.onclick = function() {
					var div = this.parentElement;
					div.remove();
				}
			}
		</script>
	</body>
</html>
```
To check if the above code is working, open `index.html` file in `D:\Learning\Docker\Projects\HtmlNginx` location (or where ever your `index.hmtl` file got created).  
It opens a webpage where you can enter tasks and click on **Add** button. It then displays the entered tasks. To remove the added task, click on **x** next to the task.

### 5.3. Create Docker File:
Next, create a file and name it as `Dockerfile` without any extension under `HTMLNGINX` project.  
Enter the following instructions in `Dockerfile` file and save it. This the basic Docker code for a simple HTML application.
```
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### 5.4. Build Docker Image:
Now, open a new terminal in VS Code and run the following command to create a docker image. In this command, the `.` indicates the current working directory where `Dockerfile` is present.
```
docker build -t html-nginx-demo:latest .
```

If you have used a different Dockerfile name, then specify it in the below command:
```
docker build -t html-nginx-demo:latest . -f <docker_file>
```
Go to **Docker Desktop** and see that `html-nginx-demo` image has been created with latest tag name under **Images** view.  
Click on `html-nginx-demo` image which displays image ID, image size and layers created to makeup this image.

### 5.5. Run Docker Image:
Now, execute the following commands to run the docker image which creates a docker container.
```
docker run --name html-nginx-demo-app -p 8080:80 html-nginx-demo
```
Go to **Docker Desktop** and see that a new container called `html-nginx-demo-app` has been created and running on port `8080` in **Containers** view.

Now, hit the URL `http://localhost:8080/` produced by the docker container which launches the web page.  
In this webpage, you should be able to create a To-do list by adding or deleting items as designed.

### 5.6. Modify HTML Code:
Let us change the webpage little bit by adding “**Launched on: 5/24/2025**” line:
* In the VS Code, open `index.html`  file.
* Change lines  
  From:
  ```
  	<body>
  		<h2>To-Do List</h2>
  		<p>Welcome User!! You can add your to-do list here..</p>
  
  		<input type="text" id="todoInput" placeholder="Enter a new task...">
  		<button onclick="newElement()">Add</button>
  ```
  To:
  ```
  	<body>
  		<h2>To-Do List</h2>
  		<p>Welcome User!! You can add your to-do list here..</p>
          <p>Launched on: 5/24/2025</p>
  
  		<input type="text" id="todoInput" placeholder="Enter a new task...">
  		<button onclick="newElement()">Add</button>
  ```
* Save changes in `index.html` file.

Open another terminal and run the following commands to stop the running container and rebuild the image file:
```
docker stop html-nginx-demo-app
docker build -t html-nginx-demo:latest .
```
In **Docker Desktop**, you can see that the image was rebuilt.  

Now, let us run the container in the detached mode (to run container at background) with the new image by executing the following command:
```
docker run --name html-nginx-demo-app1 -dp 8080:80 html-nginx-demo
```
In **Docker Desktop**, a new container called `html-nginx-demo-app1` has been created and running on port `8080` in the Docker desktop.  

Now, hit the URL `http://localhost:8080/` produced by the docker container and it launches the web application with new changes.
<br/>
 
## 6. Publish Docker Image to Docker Hub:
In order the above **HTMLNginx** Docker image (that we created) available for everyone, the image must be published to the Docker Hub. This Docker image can be shared in other cloud services as well such as **AWS**, **ECRS**, 
**Azure** Docker container.

### 6.1. Create Repository:
First, create a repository in Docker Hub:
* Login to `https://hub.docker.com/`.
* Select **Repositories** tab and click on **Create** repository.
* Under **Create repository** page:
  * Provide the Repository Name _(it is always good to keep the repository name same as the folder name)_. Here, since we are trying to publish HTML Docker application which has the Docker image name as
    `html-nginx-demo`, provide the same name as the Docker Hub repository name.
  * Select the **Visibility** as **Public** so that it is available for anyone who has access to Docker Hub.
  * Click on **Create** button.
* You can see that Docker repository has been created.	

### 6.2. Tag Image to User Repository:
When the image needs to be pushed to Docker Hub repository, it should have a specific naming convention such as `docker_user_name/docker_repo_name`.  

Go to **VS Code** and run the below command to tag the existing image name to the new image name. In these commands, replace `username` with your actual Docker Hub username:
```
docker tag html-nginx-demo username/html-nginx-demo
docker image ls
```

### 6.3. Login & Push User Repo Docker Image:
Now, run the below commands to login to Docker Hub and push the image. In these commands, replace `username` with your actual Docker Hub username:
```
docker login -u "username" docker.io 
docker push username/html-nginx-demo
```

In Docker Hub, you can see the image visible under the repository.  
In the **Repository Overview** section, click on **Add Overview** and write the information about this repository something like below. 
Here, replace my username `sriluckyus` with your actual Docker Hub username:
```
To use this application, just run a single command as below:

docker run -p 8080:80 sriluckyus/html-nginx-demo
```
Now, anyone who has access to the above repository can get the docker container running locally.

### 6.4. Run User Repo Docker Image:
Let us try to run the above Docker image in **Docker Playground** cloud platform.  
* Login to Docker Playground `https://labs.play-with-docker.com/` and click on **Start** button.
* Click on **ADD NEW INSTANCE** to create a new Docker instance 	
* Once the Docker instance is created, run the following command in the terminal. Here, replace my username `sriluckyus` with your actual Docker Hub username:
  ```
  docker run -p 8080:80 sriluckyus/html-nginx-demo 
  ``` 
* Since this docker application is running on port `8080`, click on **OPEN PORT** button and enter `8080`.
* It opens up the web application that was built where you can add or delete tasks.
<br/>

## 7. Docker Compose:
Docker Compose is a tool developed by Docker to manage multi-container Docker applications running on the single host. It simplifies the deployment of complex applications that are composed of multiple 
interconnected containers. With Docker compose, all configurations such as services, networks and volumes that are needed to setup and run the application are stored in a single configuration file. 
Then, all services with inter-connected containers are created and started with a single command from the configuration file.  
The Docker Compose configuration file has naming convention `compose.yaml` (preferred) or `compose.yml` or `docker-compose.yaml` or `docker-compose.yml` which uses YAML scripting language. 
It is important to understand the Docker Compose file structure as referred [here](https://github.com/srimarrivada/DockerCompose-FileStructure) and 
Docker Compose commands referred [here](https://github.com/srimarrivada/Docker-Commands/blob/main/README.md#7-docker-compose-commands).

### 7.1. Create Web Application:
Now, let us design a simple web application to capture student marks data with the help of Python Flask framework and load into MySQL database. 

For this application, three containes are needed which will be managed using Docker Compose:
* A container to run **Nginx** server for serving web **HTML** code and acting as a reverse proxy to forward the HTML request to Python Flask
* A container to run **Python Flask** application
* A container to run **MySQL** database

### 7.2. Stop and Delete Containers:
Before using Docker Compose, first stop all running containers, remove all containers and delete all available images along with any saved volumes just to make sure that everything is started freshly with 
Docker Compose _(though this is not mandaatory)_. 

Open a new **Command Prompt** or **Windows PowerShell** and run the following commands:  
_Stop and delete all containers:_
```
docker ps
docker stop $(docker ps -q)	
docker ps -a
docker rm $(docker ps -a -q)
```
_Delete all images:_
```
docker image ls
docker rmi $(docker image ls -q)
docker rmi -f $(docker image ls -q)
docker image ls
```
_Delete all volumes:_
```
docker volume ls
docker volume rm $(docker volume ls -q)
docker volume ls
```

### 7.3. Launch VS Code:
Open **Git Bash** command prompt, run the following commands to create a new directory at `D:\Learning\Docker\Projects` _(make sure this path exists in your location or use a different location as per your 
choice)_ and open VS Code.
```
cd "D:\Learning\Docker\Projects"
mkdir NginxFlaskMySQL
cd NginxFlaskMySQL
code .
```
It opens **VS Code** application which might prompt you to confirm if you trust the authors of file in which case, **click on Yes, I trust the authors** button.

### 7.4. Configure MySQL Database:
First, configure **MySQL** database which expects a password for `root` user and this password is stored in a file that will be passed to containers through Docker `secret` configuration.

* In **VS Code** application, click on **New Folder** icon next to `NGINXFLASKMYSQL` project under **EXPLORER** and name the folder as `mysql-db`.
* Select `mysql-db` folder and click on **New File** icon and name it as `password.txt`.
* Enter the following in `password.txt` file without any extra line and save it _(you can provide any password here but do not enter any new line)_
  ```
  R00tpwd!23
  ```

### 7.5. Configure Python Flask App:
Next, create a **Python Flask** application to render a simple **Student Form** web page and connect to MySQL to insert the submitted data through web page and display the latest 5 records available in the 
database.

#### 7.5.1. Create HTML File:
* Under `NGINXFLASKMYSQL` project in **VS Code** application, right click on the empty space and select **New Folder** and name the folder as `flask-backend`.
* Select `flask-backend` folder and click on **New Folder** icon and name it as `templates` (Note that `flask` library expects the HTML file to be available under `templates` folder in order to render the web page)
* Select `templates` folder and click on **New File** icon and name it as `student_form.html`.
* Enter the following code in `student_form.html` file and save it:
  ```
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>Student Data Entry Form</title>
    </head>

  <body style="font-family: Arial, sans-serif; background: #f2f4f8; margin: 0; padding: 40px; display: flex; flex-direction: column; align-items: center;">
  	<div>
  	  <h2 style="margin-bottom: 5px; color: #333;">Student Data Entry Form</h2>
  	  <p style="margin-bottom: 30px; color: #555;">Enter student data and submit the form below</p>
  	</div>
    <form style="background: #fff; padding: 30px; border-radius: 8px; box-shadow: 0 6px;" method="POST" action="/insertdata">
      <div>
        <label for="roll_number">Roll Number:</label>
        <input type="text" id="roll_number" name="roll_number" />
      </div><br/>
  
      <div>
        <label for="name">Name:</label>
        <input type="text" id="name" name="name" />
      </div><br/>
  
      <div>
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" />
      </div><br/>
  
      <div>
        <label for="course">Course:</label>
        <select id="course" name="course">
          <option value="btech">B.Tech</option>
          <option value="bca">B.C.A</option>
          <option value="mba">M.B.A</option>
          <option value="bsc">B.Sc</option>
        </select>
      </div><br/>
  
      <div>
        <label for="marks">Marks:</label>
        <input type="text" id="marks" name="marks" />
      </div><br/>
  
      <input type="submit" value="Submit" />
    </form>
  </body>
  </html>
  ```
The above code performs the following:
* Build a webpage with a title **Student Data Entry Form**.
* Display a form with 5 fields named `Roll Number`, `Name`, `Email`, `Course`, `Marks` and a `Submit` button.
* Configure the form as `POST` method and call `/insertdata` page up on clicking **Submit** button.

#### 7.5.2. Create Python Files:
Select `flask-backend` folder under `NGINXFLASKMYSQL` project and click on **New File** icon and name it as `flask-app.py`.  
Enter the following code in `flask_app.py` file and save it:
```
from flask import Flask, render_template, request, redirect
from db_operations import DBManager

app=Flask(__name__)

@app.route("/studentform")
def loadForm():
    return render_template('student_form.html')
    
@app.route("/insertdata", methods=['GET','POST'])
def insertData():
    if request.method == 'POST':
        id=request.form.get('roll_number')
        name=request.form.get('name')
        email=request.form.get('email')
        course=request.form.get('course')
        marks=request.form.get('marks')
        
        conn=DBManager()
        conn.insert_data(id, name, email, course, marks)
        return redirect("/querydata")
    else:
        return "There is some issue while submitting data. Please check and try again!!"

@app.route("/querydata")
def queryData():
    conn=DBManager()
    query_result=conn.query_data()

    # Prepare HTML code
    header = ['ID', 'Name', 'Email', 'Course', 'Marks', 'Submitted Date']
    table_header = '<tr>' + ''.join(f'<th style="border: 1px solid #ccc; background: #ddd;">{item}</th>' for item in header) + '</tr>'
    table_rows=''
    for row in query_result:
        table_rows = table_rows + '<tr>' + ''.join(f'<td style="border: 1px solid #ccc;">{item}</td>' for item in row) + '</tr>'
    
    response_html = f'''
        <body style="font-family: Arial, sans-serif; text-align: center;">
            <h3 style="color: green;">Data has been submitted successfully!!</h3>
            <h4>Click <a href="./studentform" style="color: blue">here</a> to submit a new record</h4>
            <h4>For your reference, 5 most recently submitted records are listed below:</h4>
            <table style="margin: auto; max-width: 500px;">
                {table_header}
                {table_rows}
            </table>
        </body>
    '''
    return response_html

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=8070)
```
The above code performs the following:
* Import `Flask`, `render_template`, `request`, `redirect methods` from `flask` library.
* Import `DBManager` class from `db_operations` module _(this module will be created later)_.
* Create three flask application routes:
  * `/studentform` route with a Python function that renders `student_form.html` file _(available in `templates` folder)_.
  * `/insertdata` route with a Python function that verifies the method. If the method is `POST`, it reads values stored in form ids named `roll_number`, `name`, `email`, `course` and `marks` and
    creates `DBManager` object to call `insert_data` method with values read from the form and then redirects to `/querydata` page.
  * `/querydata` route with a Python function that creates `DBManager` object to call `query_data` method. Then, it prepares a HTML string to display a message along with a link to submit new record
    and display a table with last 5 records submitted and finally returns the HTML string.
* Run the overall flask application on all IP addresses (0.0.0.0) and port `8070` with debug enabled.

Now, create `db_operations.py` file which is being referenced in the above `flask_app.py` file.  
Select `flask-backend` folder under `NGINXFLASKMYSQL` project and click on **New File** icon and name it as `db_operations.py`.   
Enter the following code in `db_operations.py` file and save it:
```
import mysql.connector
import os

mysql_host=os.environ.get("MYSQL_HOST")
mysql_user=os.environ.get("MYSQL_USER")
mysql_db=os.environ.get("MYSQL_DATABASE")

with open("/run/secrets/db-password","r") as file:
    mysql_pass=file.read()

class DBManager:
    def __init__(self, host=mysql_host, user=mysql_user, password=mysql_pass, database=mysql_db):
        self.connection=mysql.connector.connect(host=host, user=user, password=password, database=database)
        self.cursor=self.connection.cursor()

    def insert_data(self,id, name, email, course, marks):
        self.cursor.execute('CREATE TABLE IF NOT EXISTS Student_Marks (ID INT, Name VARCHAR(50), Email VARCHAR(50), ' \
        'Course VARCHAR(10), Marks INT, Submitted_Date DATETIME DEFAULT CURRENT_TIMESTAMP)')
        self.cursor.execute('INSERT INTO Student_Marks (ID, Name, Email, Course, Marks) VALUES (%s,%s,%s,%s,%s)',
                            (id, name, email, course, marks))
        self.connection.commit()

    def query_data(self):
        self.cursor.execute('SELECT * FROM Student_Marks ORDER BY Submitted_Date DESC LIMIT 5')
        return self.cursor
```
The above code performs the following:
* Import `mysql.connector` library.
* Get MySQL host, user and database details from environment variables.
* Get database password from `/run/secrets/db-password` file (this file will be created inside the container when it is built).
* Define `DBManager` class:
  * Create initialization method to call connect method of `mysql.connector` library by passing `host`, `user`, `password` and `database` variables.
  * Create `insert_data` method to execute `CREATE TABLE` query that creates `Student_Marks` table with `ID`, `Email`, `Course`, `Marks`, and `Submitted_Date` _(defaults to current time stamp)_ columns.
  * Create `query_data` method to execute `SELECT` query that fetches last 5 records from `Student_Marks` table ordered by `Submitted_Date` column in descending order.

#### 7.5.3. Create Requirement File:
Select `flask-backend` folder under `NGINXFLASKMYSQL` project and click on **New File** icon and name it as `requirements.txt` in which write the following lines and save the file. 
This file is used to install Python libraries specified in the file.
```
flask
mysql-connector-python
```
Here, it installs `flask` library and `mysql-connector-python` library _(mandatary for Flask to be able to connect to MySQL database)_.

#### 7.5.4. Create Docker File:
Now, create a Docker file for Python Flask application.  
Select `flask-backend` folder under `NGINXFLASKMYSQL` project and click on **New File** icon and name it as `Dockerfile`. Then, enter the following instructions in `Dockerfile file` and save it. 
```
FROM python:3-alpine
WORKDIR /flask
COPY . .
RUN pip install -r requirements.txt
ENTRYPOINT ["python", "flask_app.py"]
```
The above code performs the following:
* Pull `python:3-alpine` image if not available in local host.
* Create and set the working directory as `/flask` in container image.
* Copy all files from local host to image working directory.
* Run command to install libraries mentioned in `requirements.txt` file within image.
* Set the entry point command to run Python `flask_app.py` program when container is started.

### 7.6 Configure Web App:
Now, create a simple welcome web page that calls `student_form` page that was created earlier.
* In VS Code, right click on the empty space under `NGINXFLASKMYSQL` project, and select **New Folder** and name the folder as `web-frontend`.
* Select `web-frontend` folder and click on **New File** icon and name it as `welcome.html`.
* Enter the following code in `welcome.html` file and save it.
  ```
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Welcome</tile>
    <link rel="stylesheet" href="welcome_style.css"/>
  </head>
  
  <body>
    <div class="welcome-container">
      <h1>Welcome 👋</h1>
      <h2>This page lets you enter student details easily</h2>
      <h3>Click <a href="./studentform">here</a> to fill the form</h3>
    </div>
  </body>
  </html>
  ```
The above code performs the following:
* Build a webpage with a title `Welcome`.
* Display a message to enter student details.
* Display a web link that calls `/studentform` page at background.

Then, create a styling sheet which has been referenced in the above HTML code. 
Select `web-frontend` folder and click on **New File** icon and name it as `welcome_style.css` in which enter the following code and save it.
```
body {
  margin: 0;
  font-family: sans-serif;
  background: #f5f7fa;
  height: 100vh;
  display: flex;
  justify-content: center;
	  align-items: center;
}

.welcome-container {
  background: white;
  padding: 30px 40px;
  border-radius: 12px;
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.1);
  text-align: center;
  max-width: 750px;
  width: 90%;
}
```

### 7.7.	Configure Nginx Server:
Nginx server is used to act as a reverse proxy to forward the web app request to Python Flask application.
* In **VS Code**, right click on the empty space under `NGINXFLASKMYSQL` project, and select **New Folder** and name the folder as `nginx-proxy`.
* Select `nginx-proxy` folder and click on **New File** icon and name it as `nginx.conf`.
* Enter the following code in nginx.conf file and save it.
  ```
  server {
      listen 80;
      server_name localhost;
  
      location / {
          root /var/tmp;
          index welcome.html;
      }
  
      location /studentform {
          proxy_pass http://app:8070/studentform;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
      }
  
      location /insertdata {
          proxy_pass http://app:8070/insertdata;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
      }
  
      location /querydata {
          proxy_pass http://app:8070/querydata;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
      }
  }
  ```
The above code performs the following:
* Set the `server` to listen on port `80`.
* Set the `server_name` as `localhost`.
* Create 4 web locations:
  * `/` location that sets root directory as `/var/tmp` and calls the index page `welcome.html`.
  * `/studentform` location that forwards request to `http://app:8070/studentform` webpage.  
    **Note:** Here, `app` is the flask container name that will be created later and `8070` is the port where the flask application is configured to run.
  * `/insertdata` location that forwards request to `http://app:8070/insertdata` webpage.
  * `/querydata` location that forwards request to `http://app:8070/querydata` webpage.

### 7.8. Create Docker Compose File:
Having all applications are configured, create a Docker Compose file to create respective services for each application container and connect them through custom network.
* In **VS Code**, right click on empty space under `NGINXFLASKMYSQL` project and select **New File** icon and name it as `docker-compose.yml`.
* In `docker-compose.yml` file, write the following lines of code and save it.
  ```
  name: python-flask-mysql-demo
  services:
    db:
      container_name: mysql
      image: mysql:latest
      restart: always
      healthcheck:
        test: ['CMD-SHELL', 'mysqladmin ping --host localhost --password="$(cat /run/secrets/db-password)" --silent']
        start_period: 30s
        interval: 30s
        retries: 5
      volumes:
        - db-data:/var/lib/mysql
      secrets:
        - db-password
      environment:
        - MYSQL_ROOT_PASSWORD_FILE=/run/secrets/db-password
        - MYSQL_DATABASE=${MYSQL_DATABASE}
      expose:
        - 3306
      networks:
        - backnet
    app:
      container_name: flask
      build: 
        context: ./flask-backend
      secrets:
        - db-password
      environment:
        - MYSQL_HOST=${MYSQL_HOST}
        - MYSQL_USER=${MYSQL_USER}
        - MYSQL_DATABASE=${MYSQL_DATABASE}
      stop_signal: SIGINT # flask requires SIGINT to stop gracefully
      depends_on:
        db:
          condition: service_healthy
      networks:
        - backnet
        - frontnet
    web:
      container_name: nginx
      image: nginx:alpine
      environment:
        - FLASK_SERVER_ADDR=app:8070
      volumes: 
        - ./web-frontend:/var/tmp
        - ./nginx-proxy:/etc/nginx/conf.d
      command: /bin/sh -c "nginx -g 'daemon off;'"
      ports:
        - 8080:80
      restart: always
      depends_on: 
        - app
      networks:
        - frontnet
  
  volumes:
    db-data:
  
  secrets:
    db-password:
      file: mysql-db/password.txt
  
  networks:
    backnet:
    frontnet:
  ```
The above code performs the following:
* Set the Compose stack name as `python-flask-mysql-demo`.
* Create 3 services:
  * `db` service that sets container name as `mysql`, pulls `mysql:latest` image, restarts always in the event of failure, performs health check of container with the given test command after `30` seconds of
    container start time and at a regular internal of `30` seconds for a maximum of `5` times, maps `db-data` volume to `/var/lib/mysql` directory inside container, maps the container to `db-password` secret,
    sets environment variables for MySQL root password file and database, exposes `3306` port and maps the container to `backnet` network.
  * `app` service that sets container name as `flask`, builds image from `Dockerfile` available in `flask-backend` folder in local host, maps the container to `db-password` secret, sets environment variables
    for MySQL host, user and database, sets `stop_signal` to `SIGINT` for flask application to stop gracefully, sets dependency on `db` service to look for `service_healthy` status and maps the container to
    `backnet` and `frontnet` networks.
  * `web` service that sets container name as `nginx`, pulls `nginx:alpine` image, sets environment variables for flask server address, maps `./web-frontend` and `./nginx-proxy` directories in local host to
    `/var/tmp` and `/etc/nginx/conf.d` directories inside container, executes command to run Nginx server foreground, maps `8080` local host port to `80` container port, restarts always in the event of failure,
    sets dependency on `app` service and maps the container to `frontnet` network.
* Create `db-data` volume.
* Create `db-password` secret using `password.txt` file available in `mysql-db` folder in local host.
* Create `backnet` and `frontnet` networks.

### 7.9. Create Environment File:
Create an environment file to set MySQL host, user and database which are being referenced in the above `docker-compose.yml` file for Flask application to connect with MySQL.
In **VS Code** application, right click on empty space under `NGINXFLASKMYSQL` project, and select **New File** icon and name it as `.env` (Note that there is a `.` before `env`) and enter the following lines 
in `.env` file.
```
MYSQL_HOST=db
MYSQL_USER=root
MYSQL_DATABASE=flask_app
```

### 7.10. Start Docker Compose:
Since everything is configured, it's time to start the Docker compose which brings up respective Docker containers to create the web application.  

In **VS Code**, go to **Terminal** menu and select **New Terminal** and run the following command to start the Docker Compose:
```
docker compose up
```
In the output, you can see that:
* docker compose is pulling images for `web` and `db` services while building the image for `flask` service:
* Then, it created 2 networks named `front-net` and `back-net`, volume named `db-data` and containers named `mysql`, `flask` and `nginx`.
* At bottom, `mysql` container is creating database `flask_app` and making the database ready for connections.
* After that, `flask` container is serving `flask_app` with running on all addresses.
* At last, `nginx` container is brought up to act as reverse proxy for `flask`.

Verify if containers have been created as expected in Docker Desktop.
Launch **Docker Desktop**:
* Go to **Containers** tab where you can see that `python-flask-mysql-demo` compose stack has been created with three containers – `mysql`, `flask`, `nyginx` which are in **Running** state.
* Under **Images** tab, you can see that `nginx` and `mysql` images have been pulled and `python-flask-mysql-demo-app` image has been built. 
* Under **Volumes** tab, you can see that `python-flask-mysql-demo_db-data` volume has been created. 

### 7.11. Launch Web Application:
Now, verify if the web application is working as expected.
* Open web browser and hit `http://localhost/` or `http://localhost:8080/` URL after which you should see a **Welcome** page.
* Click on `here` in the **Welcome** page after which it displays the **Student Data Entry Form** where you can enter some data and click on **Submit** button.
* Once the form is submitted successfully, it displays a message that **Data has been submitted successfully** and displays the submitted record data.
* To enter a new record, click on `here` in the webpage and submit another 6 to 7 records. After every submission, it displays the last 5 records submitted.

### 7.12. Stop Docker Compose:
Stop Docker compose to verify if the application is down.  
Go to **VS Code** and open a **New Terminal** and run the following command to stop the Docker Compose:
```
docker compose down
```
In the output, you can see that Docker Compose has removed `nginx`, `flask` and `mysql` containers and `frontnet` and `backnet` networks.

Verify if containers and network have been removed using the following commands:
```
docker ps -a
docker network ls
```

Refresh the web browser running `http://localhost/` or `http://localhost:8080/` URL and you should see the application is down.

### 7.13. Start Docker Compose Detached:
This time, bring up the Docker compose in detached mode using the following command:
```
docker compose up -d	
```
In the output, you can see that the container logs have been suppressed and not being displayed on the console.

### 7.14.	Verify Web Application:
Refresh the web page running `http://localhost/` or `http://localhost:8080/` URL and you should see the application is up.
* In the web application, enter a new record and click on **Submit** button.
* You can see the latest record submitted along with the previously submitted records.
It means, though the containers have been re-created, it persisted old data since Docker Compose file was configured to create a volume for data storage.

### 7.15.	Validate Database:
Finally, verify if `mysql` container is working to see the data.
* Open **Command Prompt** or **Windows Powershell** and run the following command to connect to `mysql` container in interactive mode:
  ```
  docker exec -it mysql sh
  ```
* On the `sh#` prompt, run the following command to connect to the database.  
  **Note:** The database password is stored in `/run/secrets/db-password` file which has been created when the `mysql` container was built.
  ```
  mysql -uroot -p$(cat /run/secrets/db-password)
  ```
* On the `mysql>` prompt, run the following queries to verify data in the table:
  ```
  mysql -uroot -p$(cat /run/secrets/db-password)
  show databases;
  use flask_app;
  show tables;
  select * from Student_Marks;
  ```

## 8. Docker Swarm:
**Docker Swarm** or **Docker Swarm Mode** is a built-in feature of Docker Engine from version 1.12 that was introduced in June 2016. **Docker Swarm** is a container orchestration tool that allows to manage 
and deploy Docker applications across multiple Docker hosts as a single virtual system. It consists of a group of physical or virtual machines that run Docker and configured to join as a cluster. 
The activities of the cluster are managed by the **Swarm Manager**, and machines that have joined the cluster are referred to as **Nodes**. All nodes in Docker Swarm are **Docker daemons** which interact with 
each other using **Docker API**. The cluster management and orchestration features of Docker Swarm embedded in Docker Engine are built using a toolkit called [SwarmKit](https://github.com/moby/swarmkit) 
which is a separate [Moby](https://mobyproject.org/) project developed by Docker.

The key components of Docker Swarm include **Swarm Manager** which internally consists of _API_, _Orchestrator_, _Allocator_, _Scheduler_, and _Dispatcher_, **Swarm worker** which internally consists of 
_Worker_, and _Executor_. The basic Docker Swarm commands can be referred [here](https://github.com/srimarrivada/Docker-Commands/blob/main/README.md#8-docker-swarm-commands).

### 8.1.	Setup Swarm using Docker-in-Docker:
Setting up a Docker Swarm with multiple nodes on local host requires spinning up multiple virtual machines locally and Docker Swarm setup on VMs consume lot of memory _(each virtual machine with Docker running 
consumes at least 1 GB just to get started)_ and time consuming. As an alternative, we can use Docker containers to act as cluster nodes and setup Docker Swarm on these nodes.

Let us configure Docker Swarm cluster with 2 Swarm Managers _(one manager is the local host and other manager is the container acting as cluster node)_ and 3 Swarm Workers _(all workers are containers acting 
as cluster nodes)_.

### 8.2. Stop and Delete Containers:
Before setting Docker Swarm, first stop all running containers and remove all containers and delete all available images along with any saved volumes.  

Open a new **Command Prompt** or **Windows PowerShell** and run the following commands:  

_Stop and delete all containers:_
```
docker ps
docker stop $(docker ps -q)	
docker ps -a
docker rm $(docker ps -a -q)
```
_Delete all images:_
```
docker image ls
docker rmi $(docker image ls -q)
docker rmi -f $(docker image ls -q)
docker image ls
```
_Delete all volumes:_
```
docker volume ls
docker volume rm $(docker volume ls -q)
docker volume ls
```
Going forward, few Linux commands related to Docker Swarm need to be executed for which **GitBash** command prompt is required.

### 8.3. Initialize Swarm Cluster:
Open a **Gitbash** prompt and run the following commands to initialize the Docker Swarm with the local host as the Swarm Manager and setup environment variables for the token to be used to join manager and 
worker nodes and for the IP address of the master node.
```
docker swarm init --advertise-addr docker0
SWARM_MANAGER_TOKEN=$(docker swarm join-token -q manager)
echo $SWARM_MANAGER_TOKEN

SWARM_WORKER_TOKEN=$(docker swarm join-token -q worker)
echo $SWARM_WORKER_TOKEN

SWARM_MASTER_IP=$(docker info | grep -w 'Node Address' | awk '{print $3}')
echo $SWARM_MASTER_IP
```
**Note:** In the above command, `docker0` is a virtual bridge interface created by Docker during installation and it has a default IP address of `172.17.0.1`. All the Docker containers are connected to this 
bridge and use the NAT rules created by docker to communicate with the outside world.

If you don’t want to use `docker0` bridge, Docker Swarm can also be initialized using the default local host IP `127.0.0.1` using below command:
```
docker swarm init --advertise-addr 127.0.0.1
```

### 8.4. Add Manager Node:
Run the following commands to launch a Docker container and add it as a manager node to the Docker Swarm:
```
docker run -d --privileged --name manager2 --hostname=manager2 docker:dind
docker exec manager2 docker swarm join --token ${SWARM_MANAGER_TOKEN} ${SWARM_MASTER_IP}:2377
```
It takes few minutes to download the Docker image and start the container.

Once the node has joined the Swarm successfully, it displays message **This node joined a swarm as a manager**.

### 8.5. Add Worker Nodes:
Run the following commands to launch 3 Docker containers and add those as worker nodes to the Docker Swarm:
```
NUM_WORKERS=3

for i in $(seq "${NUM_WORKERS}"); do
	docker run -d --privileged --name worker${i} --hostname=worker${i} docker:dind
	sleep 5
	docker exec worker${i} docker swarm join --token ${SWARM_WORKER_TOKEN} ${SWARM_MASTER_IP}:2377
done
```
Once the above commands are executed successfully, it displays the container ID and a message **This node joined a swarm as a worker**.

Open Docker Desktop application where you can see 4 containers namely manager2, worker1, worker2 and worker3 which are in running state.

### 8.6. Start Visualizer Service:
Docker provides `visualizer` image to monitor the state of Docker Swarm cluster. This visualizer image is a sample app created by Docker Samples for learning Docker Swarm and is available in 
[Docker Hub](https://hub.docker.com/r/dockersamples/visualizer). 

Run the following command to create the visualizer service on the manager node and map to `8085` port on local host.
```
docker service create --name=viz --publish=8085:8080/tcp --constraint=node.role==manager --mount=type=bind,src=//var/run/docker.sock,dst=//var/run/docker.sock dockersamples/visualizer
```
It might take some time to complete due to the required image to be download and service to be deployed on the node.
In **Docker Desktop**, you can see a new container name prefixed with `viz` has been created and running since it was deployed on the primary manager node which is the local Docker host.

### 8.7. Verify Docker Swarm:	
Verify if the Docker Swarm cluster is up and running with 2 manager nodes and 3 worker nodes.  
Run the following command on the **Git Bash**. Note that this command works only on the manager node. 
```
docker node ls
```
In the output, it displays the information of all Docker Swarm nodes.  

The current status of Docker Swarm can also be verified using the below command which can be executed on any node:
```
docker info
```
In the output, it displays the current node information along with the Swarm status as Active with 5 Nodes of which 2 are Manager nodes.  

Verify if the **Visualizer** service is up and running using the below command:
```
docker service ls
```
Open Visualizer UI at `http://localhost:8085/` to see the Swarm cluster. At this moment, it displays single `viz` container which is a **Visualizer** service on the primary manager node.

### 8.8. Promote or Demote Node:
At any point, a worker node can be promoted as a manager and a manager node can be demoted as a worker.  
For the purpose of this demo, run the following command to promote worker1 node as manager
```
WORKER1_NODE_ID=$(docker node ls -f "role=worker" | grep 'worker1' | grep 'Ready' | awk '{print $1}')
echo $WORKER1_NODE_ID
docker node promote $WORKER1_NODE_ID
```
Verify if `worker1` node got promoted to manager using the below command:
```
docker node ls
```
You can see that **MANAGER STATUS** of `worker1` has been changed to **Reachable** which indicates it is a manager.

Now, demote this new manager node to worker using the following commands and verify its status:
```
MANAGER_NODE_ID=$(docker node ls -f "role=manager" | grep 'Reachable' | grep 'worker1' | awk '{print $1}')
echo $MANAGER_NODE_ID
docker node demote $MANAGER_NODE_ID
docker node ls
```
You can see that **MANAGER STATUS** of `worker1` has been set to blank which indicates it is a worker.

### 8.9. Deploy High availability Service:
Let us try to deploy the **Nginx** service to the cluster using High Availability Swarm configuration. 

#### 8.9.1. Launch VS Code:
Open **Git Bash** command prompt, run the following commands to create a new directory at `D:\Learning\Docker\Projects` _(make sure this path exists in your location or use a different location as per your 
choice)_ and open VS Code.
```
cd "D:\Learning\Docker\Projects"
mkdir Nginx-DockerSwarm
cd Nginx-DockerSwarm
code .
```
It opens **VS Code** application which might prompt you to confirm if you trust the authors of file in which case, click on **Yes, I trust the authors** button.  

#### 8.9.2. Create Compose File:
In **VS Code** application: 
Click on **New File** icon next to `NGINX-DOCKERSWARM` project under **EXPLORER** and name the file as `nginx.yml`.
Write the following lines of code in `nginx.yml` file and save it.
```
services:
  nginx:
    image: nginx:alpine
    deploy:
      mode: replicated
      replicas: 11
      update_config:
        parallelism: 5
        order: start-first
        failure_action: rollback
        delay: 10s
      rollback_config:
        parallelism: 0
        order: stop-first 
      restart_policy:
        condition: any
        delay: 5s
        max_attempts: 3
        window: 30s
    ports:
      - 8080:80
    healthcheck:
      test: ["CMD-SHELL", "curl http://localhost:80 || exit 1 "]
    networks:
      - nginxnetwork
  
networks:
  nginxnetwork:
```
The above code performs the following:
* Create `nginx` service that
  * Pulls `nginx:alpine` image.
  * Deploys service in `replicated` mode with:
    * `11` replicas,
    * updates `5` containers at once by starting the new task first while the running tasks briefly overlap and rollbacks containers when an update fails and updates containers at an interval of `10` seconds`,
    * rollbacks all containers simultaneously by stopping the old task before starting a new one,
    * restarts containers regardless of exit status and waits for `5` seconds between each restart attempt for maximum of `3` restart attempts and waits for `30` seconds before attempting another restart
      in case of service failure.
  * Maps local host port `8080` to container port `80`.
  * Performs healthcheck to verify web URL of nginx server and exit with `1` if it returns any failure.
  * Maps the container to `ngnixnetwork` network.
*	Create `nginxnetwork` network

#### 8.9.3. Deploy Stack:
Go to **Terminal** menu and select **New Terminal** and choose **Git Bash** from the dropdown.  
Run the following command to deploy `nginx` service on the Docker Swarm:
```
docker stack deploy nginx -c nginx.yml
```

#### 8.9.4. Verify Stack and Services:
Verify if the stack has been created using the below command:
```
docker stack ls
```
After couple of minutes, check the status of the service using the below command:
```
docker service ls
```
It displays `nginx` service created with `11` replicas.

#### 8.9.5. Verify Replicas:
Go to **Visualizer** application at `http://localhost:8085` where you can see how 11 replicas got distributed onto each node.  

Use the following command to see the list of nodes running `nginx` service:
```  
docker service ps $(docker service ls --filter name=nginx -q)
```
#### 8.9.6. Validate Task Containers:
To check the details about the respective container for a task, login to the relevant node.
* Open new **Command Prompt** or **Windows Powershell** and run the below commands to connect to `manager2` instance and verify the containers running:
  ```
  docker exec -it manager2 sh
  docker ps
  ```  
* Open new **Command Prompt** or **Windows Powershell** and run the below commands to connect to `worker1` instance and verify the containers running:
  ```
  docker exec -it worker1 sh
  docker ps
  ```  
* Open new **Command Prompt** or **Windows Powershell** and run the below commands to connect to `worker2` instance and verify the containers running:
  ```
  docker exec -it worker2 sh
  docker ps
  ```
* Open new **Command Prompt** or **Windows Powershell** and run the below commands to connect to `worker3` instance and verify the containers running:
  ```
  docker exec -it worker3 sh
  docker ps
  ```

### 8.10. Test Resilience:
Verify if the Docker Swarm can handle to unexpected problems.  

#### 8.10.1. Delete Containers:
Since there are 3 nginx containers running on the leader node, shutdown these containers using below commands to verify if Docker Swarm can detect and start new ones.  
```
docker ps
docker rm -f $(docker ps --filter name=nginx -q)
docker ps
```
Do a quick check in the **Visualizer** application where you can see 2 containers have been removed and immediately, 2 new containers have been started on the leader node as soon as the old containers were 
shutdown.

#### 8.10.2. Crash Node:
Let us try to crash a worker node.  
Run the following command to remove `worker2` container that is acting as a Swarm node.
```
docker rm -f worker2
```
The Visualizer also reports that `worker2` node is down (status changed to Red) and containers on `worker2` are removed. In few seconds, the containers that were running on `worker2` are rescheduled to other 
nodes which is visible in **Visualizer**.  

Restart the failed node by executing the following commands:
```
SWARM_WORKER_TOKEN=$(docker swarm join-token -q worker)
SWARM_MASTER_IP=$(docker info | grep -w 'Node Address' | awk '{print $3}')

i=2
docker node rm worker${i}
docker run -d --privileged --name worker${i} --hostname=worker${i} docker:dind
docker exec worker${i} docker swarm join --token ${SWARM_WORKER_TOKEN} ${SWARM_MASTER_IP}:2377
docker node ls
```
In the output, you can see that `worker2` node is added back to Swarm.  
Refresh the **Visualizer** application which displays `worker2` node is back online. Though the node is online, the containers are not rebalanced to the new worker node automatically and must be rebalanced 
manually.

First, run the following command to scale down the containers:
```
docker service scale nginx_nginx=8
```
Verify in **Visualizer** which reflects 8 containers running.  
Next, run the following command to scale up the containers:
```
docker service scale nginx_nginx=11
```
In Visualizer, you can see the containers are scheduled on `worker2` node.

### 8.11. Drain Manager Nodes:
At this moment, all nodes (both managers and workers) are running with **Active** availability and accepting new tasks from the Swarm manager (including the leader). 

Since the manager nodes should ideally be only responsible for management-related tasks, let us drain manager nodes to avoid scheduling tasks on these nodes using the below commands:
```
LEADER_NODE_ID=$(docker node ls -f "role=manager" | grep 'Leader' | awk '{print $1}')
docker node update --availability drain $LEADER_NODE_ID
REACHABLE_NODE_ID=$(docker node ls -f "role=manager" | grep 'Reachable' | awk '{print $1}')
docker node update --availability drain $REACHABLE_NODE_ID
```

Run the following command to verify if the availability of the manager node is set to **Drain**:
```
docker node ls
```
You can see that the **AVAILABILITY** of both manager nodes has been changed to **Drain**.

Since both manager nodes are updated to **Drain**, the `viz` container running on the leader manager node is stopped as there is no scaling set for the Visualizer but `ngnix` containers that were running on 
both manager nodes are subsequently stopped and reassigned to worker nodes to the maintain the scalability of 11 replicas at any given time.

Check the service status using the below command:
```
docker service ls
```
It displays **11/11** are replicated for `nginx` service which indicates it is up with all 11 replicas and **0/1** are replicated for `viz` service which indicates it is actually down.

Run the following command to check the distribution of `nginx` containers on various nodes:
```
docker service ps nginx_nginx
```
Note that at any point of time, the manager nodes can be returned to an **Active** state by executing the following commands:
```
LEADER_NODE_ID=$(docker node ls -f "role=manager" | grep 'Leader' | awk '{print $1}')
REACHABLE_NODE_ID=$(docker node ls -f "role=manager" | grep 'Reachable' | awk '{print $1}')
docker node update --availability active $LEADER_NODE_ID
docker node update --availability active $REACHABLE_NODE_ID
```
<br/>

**Congratulations!! You have successfully installed Docker and built simple application and pushed to Docker Hub.  You have also seen how to use Docker Desktop application and use Docker Compose tool to 
build multi-container application and deployed to Docker Swarm cluster.**
