pipeline {
	agent any

	environment {
		DOCKER_IMAGE='steelyhands/cw2'
		DOCKERHUB_CREDENTIALS=credentials('dockerhub')
	}

	stages {
		stage('Build from Dockerfile') {
			steps {
				script {
                    			docker.build(DOCKER_IMAGE_NAME, '-f Dockerfile .')
                		}
			}
		}

		stage('Test Docker Container') {
			steps {
				echo 'Start of Testing'
				script {
					docker.image(env.DOCKER_IMAGE).inside {
						echo 'Container built successfully'
					}
				}
			}
		}

		stage('Push to DockerHub') {
			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
				sh 'docker push steelyhands/cw2:latest'
			}
		}

		stage('Deploy to Kubernetes') {
			steps {
				sshagent(['my-ssh-key']) {
					sh "ssh -t -t ubuntu@ip-172-31-86-106 -o StrictHostKeyChecking=no \'kubectl set image deployment/cw2 --image=/steelyhands/cw2:latest'"
				}
			}
		}
	}
}
