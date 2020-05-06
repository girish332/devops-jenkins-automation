
# Deployment automation using jenkins

Created a automated pipeline using jenkins for a producation server and a testing server. 

## Getting Started

* Make sure you have docker installed
* Install the latest version of jenkins in your local system

### Prerequisites

Create two directories 

```
mkdir production
mkdir testDir
```
* Ensure that jenkins and docker are running in your local system by running the following commands
```
sudo systemctl status docker 
sudo systemctl status jenkins
```

* Create a git repo, clone it into your local system and edit the post-commit git hook
![alt text](https://github.com/girish332/devops-jenkins-automation/blob/master/images/git_hook.png?raw=true)




* Start jenkins on your local system and add the github extension by clicking on manage extensions
![alt text](https://github.com/girish332/devops-jenkins-automation/blob/master/images/manage_jenkins.png?raw=true)


### JOB 1


* Click on create a new job and in the new job configure the github link 

![alt text](https://github.com/girish332/devops-jenkins-automation/blob/master/images/job1_add_url.png?raw=true)



* For job 1 under scheduling click on poll scm scheduling and schedule it to every minute(can change it to github hook trigger but for simplicity schedule it to every minute) 

![alt text](https://github.com/girish332/devops-jenkins-automation/blob/master/images/poll_scm_shell.png?raw=true)


* In the build section execute the following script. This script will check if the production server is running on docker if not it will start the container on port 8081 and will mount /production directory in the container
```
if sudo docker ps | grep prod_server
then
echo "Prod server already running"
else
sudo docker run -d -t -i -p 8081:80 -v /home/girish332/Desktop/devops/production:/usr/local/apache2/htdocs/ --name prod_server httpd
fi
sudo cp -r -v -f * /home/girish332/Desktop/devops/production
```


### JOB 2

* For the second job create a branch of the original repo dev1 by using the command 
```
git branch dev1
```
* Create new job in jenkins and set the branch to dev1

![alt text](https://github.com/girish332/devops-jenkins-automation/blob/master/images/job2_1.png?raw=true)



* Set the scheduling to pollscm and set the post build command to the following
```
if sudo docker ps | grep test_server
then
echo "Test server already running"
else
sudo docker run -d -t -i -p 8082:80 -v /home/girish332/Desktop/devops/testDir:/usr/local/apache2/htdocs/ --name test_server httpd
fi
sudo cp -r -v -f * /home/girish332/Desktop/devops/testDir/
```
* This script will check if the docker test server is running if not it will start the docker container and copy the contents to the server.

![alt text](https://github.com/girish332/devops-jenkins-automation/blob/master/images/job2_2.png?raw=true)



### JOB 3


* For JOB 3 create a new job and under soruce code managment set the branch to dev1 and do as shown to merge before build

![alt text](https://github.com/girish332/devops-jenkins-automation/blob/master/images/job3_1.png?raw=true)


* Set the build trigger to build the project after job2 is built

![alt text](https://github.com/girish332/devops-jenkins-automation/blob/master/images/job3_2.png?raw=true)


* In the post build options build job1 if the build is stable and add the following configration

![alt text](https://github.com/girish332/devops-jenkins-automation/blob/master/images/job3_3.png?raw=true)


### All jobs running 

![alt text](https://github.com/girish332/devops-jenkins-automation/blob/master/images/job_run.png?raw=true)

