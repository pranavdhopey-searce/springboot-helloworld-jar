pipeline {
    agent any
    environment {
        PROJECT_ID = 'searce-playground'
        CLUSTER_NAME = 'pranav-cluster'
        LOCATION = 'asia-soujth1-a'
        CREDENTIALS_ID = 'searce-playground'
    }

    tools {
        maven "maven3"
    }

    stages {
        stage('Clone') {
            steps {
                // Get the code from a GitHub repository
                git 'https://github.com/pranavdhopey/springboot-helloworld-jar.git'
            }
        }
		
        stage('Build') {
            steps {		
	        // Run Maven on a agent.
                sh "mvn clean install"
            }
        }
		
	stage('Building image') {
            steps {
                script {
                   dockerImage = docker.build("asia.gcr.io/${env.PROJECT_ID}/pranavweb/springapp:${env.BUILD_ID}")
                }
            }
        }

	stage('Pushing image') {
            steps {
	        script {
                    withDockerRegistry(credentialsId: 'gcr:searce-playground', url: 'https://asia.gcr.io') {
    	                dockerImage.push("${env.BUILD_ID}")
		    }	
                } 
            }
        }

	stage('Deploy to GKE Dev Namsespace') {
            steps {
	        sh "sed -i 's/latest/${env.BUILD_ID}/g' Manifest/deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, zone: env.LOCATION, manifestPattern: 'Manifest/', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }     
        }
    }
}
