<center><h1>  Continuos Integration & Continuous Delivery </h1></center>

#### Introduction 
<span style='color:Green'>There is no world without human errors</span>. So CI/CD Comes to save us, the story of building new software starts from coding, you write code and your colleague write another piece of code, After that two pieces are merged together and then unit testing are start and infrastructure is created. 
- When we talk about `CI` Think about <span style="color:rgb(255, 0, 0)">code</span>. 
- When we talk about `CD` Think about <span style="color:rgb(255, 0, 0)">Deployment</span>. 

![[DOC-00-Devops/img/3.png]]

#### Definitions 
- **Continuous Integration:** Software Development practice For building and testing code every time team member push code to source control. 
- **Continuous Deployment**: Automated process of releasing every code change to production. 
- **Single Responsibility principal**: mean that every function is responsible for one job at the time. 
#### Jenkins 
- is continuous integration server only, but we can exten it with blugins. 
- Jenkins Architecture 
	![[jenkinsarchitect.png]]
- Jenkins workflow 
	- Jenkins <u>Listen to Specific Repo</u> or <u>use web hocks</u> in github and based on specific Action or even scheduled it check or pull code changes from team Repo. 
	- Run work flow (Some Tasks) which may be Auto build process (Maven, Apache ANT) OR Auto Test / Unit Testing. 
	- Reporting and Notification(Success \ Fails)
- Jenkins home locations 
	```txt 
	/var/jenkins_home/
	```
- **Jenkins Webhook** is a feature in the Jenkins automation server that allows external systems or services to trigger builds in Jenkins. 
- Deploying Jenkins in EC2 Instance 
	```bash 
	   sudo apt update
	   
	   sudo apt install openjdk-17-jre
	   
	   java -version
	    
	   curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
	     /usr/share/keyrings/jenkins-keyring.asc > /dev/null
	   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
	     https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
	     /etc/apt/sources.list.d/jenkins.list > /dev/null
	   
	   sudo apt-get update
	   
	   sudo apt-get install jenkins
	   
	   sudo systemctl start jenkins.service
	   
	   sudo systemctl status jenkins
	   # You can access jenkins in "machine-ip:8080"
	```

- Jenkins with docker 
	```bash 
	  docker volume create jenkins
	  docker run -itd --name jenkins -p 8080:8080 -p 50000:50000 -v jenkins:/var/jenkins_home jenkins/jenkins
	```
- Building Jenkins Container which have access to the host docker daemon which will use the docker host as an agent.
	```bash 
	  sudo docker run -p 8080:8080 -p 50000:50000 -d \
	   -v jenkins_home:/var/jenkins_home \
	   -v /var/run/docker.sock:/var/run/docker.sock \
	   -v $(which docker):/usr/bin/docker --name [container_name] jenkins/jenkins:lts
	```
- Access Jenkins 
	```txt
	  http://localhost:8080
	```
-  you will be prompted to enter a password, which you can find in the Jenkins logs
	```bash
	  docker logs jenkins 
	  # Cat providing file
	  cat /var/jenkins_home/secrets/initialAdminPassword
	```
- **Recommended plugins** 
	1. **Git** allows you to integrate GitHub to clone repository (this is one of the suggested plugins available at install)
	2. **Github Pull Request Builder** builds pull requests in GitHub and reports results
	3. **Thinbuckup** 
	4. **Maven** used in building Java projects, Maven uses a declarative XML file called "pom.xml" to define the project's dependencies, build process, and other configuration settings 
	   Use Maven Pluging for 
		   1. Reduce configuration Required for maven projects 
		   2. Post build deployment artifact 
		   3. Test reporting 

	5. [Role-based Authorization Strategy](https://plugins.jenkins.io/role-strategy)
	   The Role Strategy plugin is meant to be used from [Jenkins](https://jenkins.io) to add a new role-based mechanism to manage users' permissions.
	   Manage Jenkins > Security > Authorization > Role base Strategy 
	   After Enable this option in security dashboard you will see new item named " Managed and Assign Roles"
- Inject Secrets in build 
	1. You need the following plugin to be installed
		- Credential plugin 
		- Credential Binding plugin. 
	2. Define the secretes which you want in jenkins credential section. 
	3. use `withCredential` statement in the pipelines within the sate steps, Example. 
		```bash 
		   withCredentials([usernamePassword(credentialsId: 'mycompany-github-creds',  passwordVariable: 'pass', usernameVariable: 'user')]) {
		       // the code here can access $pass and $user
		   }
		```
		Note that you have only access theses environment with the withcredntial blopck. 
	4. Simple but powerfull example 
		```groovy
		   pipeline {
		       agent any
		   
		       stages {
		           stage('Hello') {
		               
		               steps {
		                   withCredentials([usernamePassword(credentialsId: 'DATABASE', passwordVariable: 'db_pass', usernameVariable: 'db_user')]) {
		                     echo "hellow $db_user"
		                     echo "Password is $db_pass"
		                  }
		                   echo 'Hello World'
		     
		               }
		           }
		       }
		   }
		```

#### Q&A 
1. <span style='color:red'>What is the benfints of CICD? </span>  
	  - For Business. 
	    1. Reduce cost.  (much time = much cost)
	    2. Faster time to market. (competence)
	  * For developer and devops team. 
	    1. Increase productivity. 
	    2. Reduce Risk and human errors. 
	    3. Rollback Faster. 
2. <span style="color:rgb(255, 0, 0)">What is the difference between continuous Deployment and continuous Delivery?</span> 

	![[continuousdeliverydeployment-img-000.png]]
