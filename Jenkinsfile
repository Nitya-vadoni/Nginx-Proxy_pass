pipeline {
    agent any

    environment{
      DOCKER_USER = "nItyavadoni"
      NODE_IMAGE = "${DOCKER_USER}/Node-app"
      NGINX_IMAGE = "${DOCKER_USER}/Nginx"
    }

    stages {
        stage('Building docker images ') {
            steps {
                sh 'docker build -f Dockerfile-Node -t Node-app:latest .'
                sh 'docker build -f Dockerfile-Nginx -t Nginx:latest .'
                
            }
        }

        stage('Login to Dockerhub ') {
            steps {
                 withCredentials([usernamePassword(credentialsId: 'dockerhub-cred',
                usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
            }
        }
        }
        stage('Push images to Dockerhub') {
            steps {
                sh 'docker push $NODE_IMAGE'
                sh 'docker push $NGINX_IMAGE'
            }
        }
    }
}
