
- Recommended time duration for this demo: 2.5 hours
- Required Cloud Resource: ECS, EIP
- Participants to prepare: Image (screenshot) of current resume

Preview of the chatflow AI Agent:
![[Pasted image 20250424151836.png]]

- ECS Recommended configuration
	- vCPU: 4-8
	- Memory: 16-32GB
	- OS: Ubuntu
	- EIP: Traffic bandwidth size (100 Mbit/s)

## 1. Create ECS

![[Pasted image 20250424153944.png]]
![[Pasted image 20250424154057.png]]
![[Pasted image 20250424154130.png]]
- custom config
- billing mode: pay-per-use
- Region: Brazil
- AZ: Random
- Instance: General computing-plus x1e, x1e.8u.32g, 8 vCPUs	, 32 GiB, 
- OS: Ubuntu 24.04
- system disk: 40GB
- Network: use your own VPC & subnet
- Security group: default
- Public Network Access: EIP, Auto Assign, Traffic bandwidth size (100 Mbit/s), release with ECS
- ECS Name: you can decide
- Login mode: password
- Enterprise project: can select default

After the ECS is created and running, remote login into ECS:
![[Pasted image 20250424155052.png]]
need to edit security group:
![[Pasted image 20250424155216.png]]
![[Pasted image 20250424155247.png]]
![[Pasted image 20250424155344.png]]

After login into ECS:
![[Pasted image 20250424155437.png]]

## 2. Download Ollama

[Download Ollama on Linux] ([https://ollama.com/download/linux](https://ollama.com/download/linux))

Install with below command:
```
curl -fsSL https://ollama.com/install.sh | sh
```

![[Pasted image 20250424155711.png]]
After installation is completed, verify ollama version
```
ollama -v
```
![[Pasted image 20250424155944.png]]
## 3. Download and run DeepSeek Distilled models

[deepseek-r1] ([https://ollama.com/library/deepseek-r1](https://ollama.com/library/deepseek-r1) )

**DeepSeek-R1-Distill-Qwen-1.5B**
```
ollama run deepseek-r1:1.5b
```

**DeepSeek-R1-Distill-Qwen-7B**
```
ollama run deepseek-r1:7b
```

**DeepSeek-R1-Distill-Llama-8B**
```
ollama run deepseek-r1:8b
```

**DeepSeek-R1-Distill-Qwen-14B**
```
ollama run deepseek-r1:14b
```

This demo we choose `deepseek-r1:7b`
![[Pasted image 20250424160050.png]]
![[Pasted image 20250424160922.png]]

to exit chatbot mode:
```
/exit
```


## 4. Download and install Docker compose

[Ubuntu | Docker Docs]([https://docs.docker.com/engine/install/ubuntu/]

### 4.1 Uninstall old versions

Run the following command to uninstall all conflicting packages:
```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

### 4.2 Install using the apt repository

Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker apt repository. Afterward, you can install and update Docker from the repository.

Set up Docker's apt repository:
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
sudo apt-get update
```

### 4.3 Install the Docker packages

To install the latest version, run:
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verify that the installation is successful by running the hello-world image:
```
sudo docker run hello-world
```

This command downloads a test image and runs it in a container. When the container runs, it prints a confirmation message and exits:
![[Pasted image 20250424161635.png]]


## 5. Deploy Dify with Docker Compose

### 5.1 Clone Dify

```
# Assuming current latest version is 0.15.3
git clone https://github.com/langgenius/dify.git --branch 0.15.3
```
![[Pasted image 20250424161707.png]]

### 5.2 Starting Dify

Navigate to the Docker directory in the Dify source code:
```
cd dify/docker
```

Copy the environment configuration file
```
cp .env.example .env
```

### 5.3 Start the Docker containers

```
docker compose up -d
```
![[Pasted image 20250424161748.png]]
![[Pasted image 20250424162019.png]]

Finally, check if all containers are running successfully:
```
docker compose ps
```

![[Pasted image 20250424162117.png]]

With these steps, you should be able to install Dify successfully.


### 5.4 Access Dify

```
EIP/install
```

![[Pasted image 20250424162240.png]]

![[Pasted image 20250424162259.png]]

Setup and sign in

Home page (Studio) for Dify
![[Pasted image 20250424162339.png]]


## 6. Link Ollama and other LLM service provider to Dify

Go to settings
![[Pasted image 20250424162502.png]]

Model Providers
![[Pasted image 20250424162517.png]]

### 6.1 Ollama + DeepSeek

[Integrate Local Models Deployed by Ollama | Dify](https://docs.dify.ai/development/models-integration/ollama)

Search for ollama, add model
- model name: deepseek-r1:7b
- Base URL: `http://{Private IP}:11434`
- Completion mode: Chat
- Model context size: 4096
- Upper bound for max tokens: 4096
- Vision support: No
- Function call support: No

#### 6.1.1 If you are using docker to deploy Dify and Ollama, you may encounter the following error
![[Pasted image 20250424163414.png]]
```
httpconnectionpool(host=127.0.0.1, port=11434): max retries exceeded with url:/cpi/chat (Caused by NewConnectionError('<urllib3.connection.HTTPConnection object at 0x7f8562812c20>: fail to establish a new connection:[Errno 111] Connection refused'))

httpconnectionpool(host=localhost, port=11434): max retries exceeded with url:/cpi/chat (Caused by NewConnectionError('<urllib3.connection.HTTPConnection object at 0x7f8562812c20>: fail to establish a new connection:[Errno 111] Connection refused'))
```

This error occurs because the Ollama service is not accessible from the docker container. `localhost` usually refers to the container itself, not the host machine or other containers.

You need to expose the Ollama service to the network to resolve this issue.

 Setting environment variables on Linux
 If Ollama is run as a systemd service, environment variables should be set using `systemctl`:
 
 Edit the systemd service by calling `systemctl edit ollama.service`. This will open an editor.
 For each environment variable, add a line `Environment` under section `[Service]`:
```
[Service]
Environment="OLLAMA_HOST=0.0.0.0"
```

Save and exit.(ctrl+o, enter, ctrl+x)
Reload `systemd` and restart Ollama:
```
systemctl daemon-reload
systemctl restart ollama
```

After editing, you can add ollama model into Dify
![[Pasted image 20250424164051.png]]



### 6.2 Zhipu AI
![[Pasted image 20250424164207.png]]

![[Pasted image 20250424164226.png]]

Use your email to signup for a free account and get free token to use its LLM (obtain the API key)
![[Pasted image 20250424164359.png]]

Copy the api key to Dify and now you have deepseek and zhipu AI setup successfully.
![[Pasted image 20250424164434.png]]


## 7. Add a Knowledge base

![[Pasted image 20250424164625.png]]

We can upload some example resume template to this knowledge base, you can search from google to obtain Harvard university resume and cover letter template to upload into this knowledge base.

![[Pasted image 20250424164824.png]]

Save and Process

![[Pasted image 20250424164902.png]]


## 8. Create a Chatflow (Career Coach & Resume Optimizer)

Go to Studio, create from blank
![[Pasted image 20250424164937.png]]

![[Pasted image 20250424165054.png]]

![[Pasted image 20250424170002.png]]


### 8.1 Add a question classifier

![[Pasted image 20250424170105.png]]

Connect deepseek LLM to class 1 of the question classifier

![[Pasted image 20250424170222.png]]

Edit class:
1. Class 1: Career Coach
2. Class 2: Resume Optimizer
3. Advanced Settings:
	1. use chatgpt to generate instructions to help the classifier better understand the questions

![[Pasted image 20250424170852.png]]


### 8.2 Edit Career Coach LLM 

1. Change LLM name to `Career Coach`
2. SYSTEM instruction: use chatgpt to generate instructions to let the LLM know what you want the LLM to do
![[Pasted image 20250424171232.png]]


### 8.3 Add Zhipu AI LLM for resume optimizer

From question classifier Class 2, click + sign and add a new LLM module,
![[Pasted image 20250424171412.png]]

1. change the name to Resume Optimizer
2. change the model to Zhipu AI `glm-4v-flash` or `glm-4v-plus`
![[Pasted image 20250424171539.png]]

3. SYSTEM instruction: use chatgpt to generate instructions to let the LLM know what you want the LLM to do
4. Enable VISION function so that LLM can scan and analyze the uploaded image of resume
![[Pasted image 20250424171749.png]]


add following answer from the `Resume Optimizer` LLM 
![[Pasted image 20250424171842.png]]

![[Pasted image 20250424171933.png]]


## 9. Add the knowledge base before the Resume Optimizer LLM

Click the + sign between the questions classifier and Resume optimizer to add a `Knowledge Retrieval` module
![[Pasted image 20250424172112.png]]

![[Pasted image 20250424172310.png]]

![[Pasted image 20250424172328.png]]

![[Pasted image 20250424172344.png]]



## 10. Test it out

![[Pasted image 20250424172421.png]]

Click the top right corner Preview, click the Manage button
![[Pasted image 20250424172503.png]]

Enable the "File Upload" function
![[Pasted image 20250424172535.png]]


After completed the chat flow configuration, click the preview button to test out the function of the AI application

The question classifier will automatically recognize the question context and choose the appropriate LLMs accordingly


