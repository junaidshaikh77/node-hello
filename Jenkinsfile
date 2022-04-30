pipeline {
    agent any
	environment{
	    DOCKER_TAG = getDockerTag()
    }
	stages{
	    stage('Build Docker Image'){
		    steps{
			    sh "docker build . -t junaidshaikhcr7/testrepo:node-docker"
			}
		}
		stage('DockerHub Push'){
		    steps{
			    withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
				    sh "docker login -u junaidshaikhcr7 -p 123456789 ${dockerHubPwd}"
					sh "docker push junaidshaikhcr7/testrepo:${DOCKER_TAG}"
				}
			}
		}
        stage('Deploy to k8s'){
            steps{
			    sh "chmod +x changeTag.sh"
				sh "./changeTag.sh ${DOCKER_TAG}"
				sshagent(['k8s-cluster']) {
				    sh "scp -o StrictHostKeyChecking=no service.yaml node-pod.yaml ubuntu@3.110.116.219:/home/ubuntu"
				    script{
				        try{
					        sh "ssh ec2-user@3.110.116.219 kubectl apply -f ."
					    }catch(error){
					       sh "ssh ec2-user3.110.116.219 kubectl create -f ."
						}
					}   
				}
      }				
	  }
	}
}
