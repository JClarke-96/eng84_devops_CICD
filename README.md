# What is CICD
## Continuous Integration (CI)
Continuous Integration (CI) is the practice of automating the integration of code changes from multiple contributors into a single software project.

## Continuous Delivery (CD)
Continuous Delivery (CD) is a software engineering approach in which teams produce software in short cycles, ensuring that the software can be reliably released at any time and, when releasing the software, without doing so manually. It aims at building, testing, and releasing software with greater speed and frequency.

## Continuous Deployment (CD)
Continuous Deployment (CD) is a software release process that uses automated testing to validate if changes to a codebase are correct and stable for immediate autonomous deployment to a production environment.

![alt text](https://3ovyg21t17l11k49tk1oma21-wpengine.netdna-ssl.com/wp-content/uploads/2015/03/cicdcd.png)

## CICD
### What are the benefits?
- Smaller code changes are simpler with fewer unintended consequences
- Fault isolation is simpler and quicker
- Faster rate of release

### What tools are used?
- Jenkins
- CircleCI
- AWS CodeBuild
- Azure DevOps
- Atlassian Bamboo
- Travis CI

# Continuous Integration (CI)
## Jenkins
- Discard old builds | Strategy `Log Rotation` Max # of builds `2`
- GitHub project | Project url: `https://github.com/JClarke-96/eng84_devops_CICD.git/`
- Restrict where project can run | Label Expression `sparta-ubuntu-node`
- Git Repositiories | Repository URL `git@github.com:JClarke-96/eng84_devops_CICD.git`
- Git Repositories | Credentials `jordan-key`
- Git Branches to build | Branch Specifier `*/main`
- Build Triggers | GitHub hook trigger for GITScm polling
- Provide Node & npm bin/ folder to PATH | NodeJS Installation `Sparta-Node-JS`
- Build
	```
	cd app
	npm install
	npm test
	```
- Build other projects | Projects to build `eng84_jordan_merge`

## GitHub
- Webhook | Payload URL `http://JenkinsIP/github-webhook/`
- Webhook | Content type `application/json`
- Deploy keys | CICD `public key`

# Continuous Delivery (CD)
## Jenkins Merge
- Discard old builds | Strategy `Log Rotation` Max # of builds `2`
- GitHub project | Project url `https://github.com/JClarke-96/eng84_devops_CICD.git/`
- Restrict where this project can be run | Label Expression `sparta-ubuntu-node`
- Git Repositiories | Repository URL `git@github.com:JClarke-96/eng84_devops_CICD.git`
- Git Repositories | Credentials `jordan-key`
- Git Branches to build | Branch Specifier `*/dev`
- Provide Node & npm bin/ folder to PATH | NodeJS Installation `Sparta-Node-JS`
- Git Publisher | Push Only If Build Succeeds
- Git Publisher | Branch to push `main`
- Git Publisher | Target remote name `origin`
- Build other projects | Projects to build `eng84_jordan_deploy`

## Jenkins Deploy
- Discard old builds | Strategy `Log Rotation` Max # of builds `2`
- Restrict where this project can be run | Label Expression `sparta-ubuntu-node`
- Provide Node & npm bin/ folder to PATH | NodeJS Installation `Sparta-Node-JS`
- SSH Agent | Credentials `DevOpsStudent`
- Build
	```
	rm -rf eng84_devops_CICD*
	git clone -b main https://github.com/JClarke-96/eng84_devops_CICD.git
	cd eng84_devops_CICD

	rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@publicIP:/home/ubuntu/app
	rsync -avz -e "ssh -o StrictHostKeyChecking=no" environment ubuntu@publicIP:/home/ubuntu/app

	ssh -A -o "StrictHostKeyChecking=no" ubuntu@publicIP <<EOF
    	# 'kill' all running instances of node.js
    	killall npm
    
    	# run provisions file for dependencies
    	cd /home/ubuntu/app/environment/app
    	chmod +x provision.sh
    	./provision.sh
    
    	# Install npm for remaining dependencies
    	cd /home/ubuntu/app/app
    	sudo npm install
    	node seeds/seed.js
     
    	# Run the app
    	node app.js &

EOF
	```

# Test 5