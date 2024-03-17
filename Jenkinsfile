
pipeline {
	agent any
	environment { 
		DOCKERHUB_CREDENTIALS = credentials('jorge-dockerhub')
		IMAGENAME = "jcasillas245a/reto_final_python"
		DOCKERIMAGE = ''
		}
	stages {
		stage('Installation') {
			parallel {
				stage('Build Docker Image') {
					steps {
						sh 'docker build -t docker-imagen .'
					}
				}
				stage('Test programm') {
					agent {
						docker {
							image 'python:3.11-slim'
							args '-u root --privileged'
						}
					}
					stages {
						stage('apt install') {
							steps {
								script {
									sh 'apt-get update && apt-get install -y pkg-config'
        						}
							}
						}
						stage('pip install') {
							steps {
								sh 'pip install -r requirements.txt -r requirements_venv.txt'
							}
						}
						stage('Coverage & Test') {
							steps {
								sh 'pytest --cov=app tests/'
								sh 'coverage report -m'
							}
						}
						stage('Linting') {
							steps {
								sh 'flake8'
							}
						}
					}
				}
			}
		}
		stage('Login & Push ') {
			when {
                    branch "main/develop"
                }
			steps {
				sh """
					echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
					docker tag docker-image $IMAGENAME
					docker push $IMAGENAME

				"""
			}
		}
	}	
}
