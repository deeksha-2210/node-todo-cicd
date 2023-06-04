Jenkins CICD with GitHub Integration

Problem Statement: A node js application is there. You need to containerize it using docker file so that it can run on any platform . This is CI. Now for CD,  you have to deliver it using Aws ec2. For this you need to create a jenkins pipeline.

Tools: GitHub, Docker, Jenkins, AWS

Steps:

Note: if jenkins is not getting installed follow this link(https://pkg.jenkins.io/debian/)

	EC2 instance
		○  Create AWS EC2 instance
		○  sudo apt update
		○  sudo apt install openjdk-11-jre
		○  java -version
		○  curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \   /usr/share/keyrings/jenkins-keyring.asc > /dev/null 
		○  echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \   https://pkg.jenkins.io/debian binary/ | sudo tee \   /etc/apt/sources.list.d/jenkins.list > /dev/null
		○  sudo apt-get update 
		○  sudo apt-get install jenkins
		○  sudo systemctl enable jenkins
		○  sudo systemctl start jenkins
		○  sudo systemctl status jenkins
		○  sudo cat /var/lib/jenkins/secrets/initialAdminPassword
		○  history
		○  sudo apt install docker.io
		
	Jenkins
		○ Create a freestyle project
		○ Add description
		○ Add Github link
		
	Generate ssh key in ec2
		○ ssh-keygen
		○ Press enter 2-3 times
		○ Cd .ssh
		○ You will find id_rsa id_pub
		
	Connect github to jenkins (use public key (id_rsa.pub))
		○ Go to setting in github-> SSH keys &GPG keys-> new SSH key-> copy and paste id_rsa.pub here
		○ Kind-> SSH keys
		○ ID- //anything just a name
		○ Description // add anything
		○ Username-> ubuntu // name of your instance
		○ Private key-> id_rsa [ copy from **** to end***]
		
	Containerize the application(Docker)
		○ sudo apt install docker.io -y
		○ Sudo vim Dockerfile
			
		FROM node:12.2.0-alpine  //install OS
		WORKDIR app               //    created app directory
		COPY . .                         //copy the code
		RUN npm install             //install npm
		EXPOSE 8000
		CMD ["node","app.js"]    //run node app
		
		○ docker build . -t node-app
		○ sudo usermod -a -G docker $USER //giving permission to user for docker
		○ Sudo reboot
		○ docker run -d --name node-todo-app -p 8000:8000 todo-node-app
		○ Edit inbound rules in ec2 add 8000 tcp 
			
	Jenkins
		○ Go to jenkins job
		○ Execute shell 
		○ docker build . -t node-app-todo
		○ docker run -d --name node-app-container -p 8000:8000 node-app-todo
		
	Create webhook ( any changes in github will trigger jenkins automatically)
		○ Go to jenkins-> manage jenkins-> plugins-> github integration
		○ Go inside your repo-> settings-> webhooks
		○ Payload url //your jenkins url [make sure tick is there]
		○ Go to jenkins-> your project-> configure-> build triggers
		○ Tick mark Github hook trigger for GITSCM polling-> save
  
	Summary:
	1. Created ec2 instance using ubuntu.
	2. Installed jenkins
	3. Connected jenkins with github using ssh keys
	4. Created Docker file to containerise the application
	5. Build the docker image and created the container
	6. Now build the image using jenkins
	7. Create a webhook to trigger jenkins of any changes are made in github
  
  ![output](https://github.com/deeksha-2210/node-todo-cicd/assets/101381027/3ca94bcd-aa35-4db5-a3c5-cde4d5b0fe73)

