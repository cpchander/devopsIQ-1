pipeline {
    agent {
        node {
            label 'slave-2'
        }
    }
    parameters {
    	string(name: 'serverIP', defaultValue: 'None', description: '34.131.73.46')
	string(name: 'servername', defaultValue: 'None', description: '34.131.195.216')
	password(name: 'dockerpass', description: 'Master@3419')	    
    }
    stages {
        stage('SCM checkout'){
            steps {
		git "https://github.com/cpchander/devopsIQ-1.git"
            }
	}
	stage('Remove dockers'){
	    steps {
		sh "if [ `sudo docker ps -a -q|wc -l` -gt 0 ]; then sudo docker rm -f \$(sudo docker ps -a -q);fi"
		}
	}
	stage('Build'){
	    steps {
		    sh "sudo docker build /home/cpchander/jenkins/workspace/${JOB_NAME} -t cpchander/devopsdemo"
	   }
	}
	stage('Docker Push'){
		steps {
		    sh "sudo docker login --username cpchander --password ${dockerpass}"
                    sh "sudo docker push cpchander/devopsdemo:latest"
	        }
	}
	stage('Configure servers with Docker and deploy website') {
            	steps {
			sh 'ansible-playbook docker.yaml -e "hostname=${servername}"'
            	}
        }
	stage('Install Chrome browser') {
            	steps {
                	sh 'ansible-playbook chrome.yaml -e "hostname=localhost"'
            	}
        }
	stage ('Testing'){
		steps {
			sh "sudo apt install python3-pip -y"
			sh "pip3 install selenium"
			sh "python3 selenium_test.py ${params.serverIP}"
		}
	}
    }
}
