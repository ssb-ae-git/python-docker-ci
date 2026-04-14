pipeline {
    agent { label 'my_Slave' }

    environment { 
        PATH = "/usr/local/bin:/usr/bin:${env.PATH}"
        DOCKER_IMAGE = "ssbaedocker/python-ci-lab"
        IMAGE_TAG = "v1.0.0"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/ssb-ae-git/python-docker-ci.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$IMAGE_TAG .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$IMAGE_TAG'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
		sh '''
		docker rm -f python-container || true
		docker run -d -p 5000:5000 --name python-container python-ci-lab
		'''
            }
        }
    }
}
