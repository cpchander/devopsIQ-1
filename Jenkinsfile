pipeline {
    agent {
        node {
            label 'slave-1'
        }
    }
    parameters {
    	string(name: 'serverIP', defaultValue: 'None', description: 'Enter Server IP ')
	string(name: 'servername', defaultValue: 'None', description: 'Enter Ansible slave name ')
	password(name: 'dockerpass', description: 'Enter docker login password ')

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
		    sh "sudo docker build /home/cpchander/workspace/${JOB_NAME} -t cpchander/devopsdemo"
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
			sh 'sudo ansible-playbook /home/cpchander/workspace/mypipeline/docker.yaml -e "hostname=${servername}"'
            	}
        }
	stage('Install Chrome browser') {
            	steps {
                	sh 'sudo ansible-playbook /home/cpchander/workspace/mypipeline/chrome.yaml -e "hostname=localhost"'
            	}
        }
	stage ('Testing'){
		steps {
			sh "sudo apt install python3-pip -y"
			sh "pip3 install selenium"
			sh "python3 /home/cpchander/workspace/mypipeline/selenium_test.py ${params.serverIP}"
		}
	}
    }
}
