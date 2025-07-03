pipeline {
    agent { label 'slave-1' }	

    tools {
        // Install the Maven version configured as "M3" and add it to the path adding this line to check my webhook.
        maven "maven"
    }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dock-password')
	} 
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/Rohitpotdar01/star-agile-health-care.git'
                //git 'https://github.com/Rohitpotdar01/star-agile-health-care'
            }
		}
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
		}
       stage("Docker build"){
            steps {
				sh 'docker version'
				sh "docker build -t rohitpotdar/healthcare-eta-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag rohitpotdar/healthcare-eta-app:${BUILD_NUMBER} rohitpotdar/healthcare-eta-app:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Push2DockerHub') {

			steps {
				sh "docker push rohitpotdar/healthcare-eta-app:latest"
			}
		}
        stage('Deploy to Kubernetes using ansible Dev Environment') {
            steps {
		script {
		sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible_Server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /etc/ansible/deploy_docker.yml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
		       }
            }
    	}
    }
}
