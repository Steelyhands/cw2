pipeline {
	agent any

	environment {
		DOCKER_IMAGE_NAME='steelyhands/cw2'
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
				echo 'Starting Test'
				script {
					docker.image(env.DOCKER_IMAGE_NAME).inside {
						echo 'Container built successfully'
					}
				}
			}
		}

		stage('Push to DockerHub') {
			steps {
				echo 'pushing to dockerhub'
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
				sh 'docker push steelyhands/cw2:1.0'
			}
		}

		stage('Deploy to Kubernetes') {
			steps {
				echo 'deploying to kubernetes'
				sshagent(['my-ssh-key']) {
					sh "ssh -t -t ubuntu@ip-172-31-63-200 -o StrictHostKeyChecking=no \'kubectl set image deployment/cw2 cw2=steelyhands/cw2:1.0'"
				}
			}
		}
	}
}
