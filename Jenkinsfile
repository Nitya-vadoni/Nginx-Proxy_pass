pipeline {
    agent any

    environment{
      DOCKER_USER = "nityavadoni"
      NODE_IMAGE = "${DOCKER_USER}/node-app"
      NGINX_IMAGE = "${DOCKER_USER}/nginx"
    }

    stages {
        stage('Building docker images ') {
            steps {
                sh 'docker build -f Dockerfile-Node -t nityavadoni/node-app:latest .'
                sh 'docker build -f Dockerfile-Nginx -t nityavadoni/nginx:latest .'
                
            }
        }

        stage('Login to Dockerhub ') {
            steps {
                 withCredentials([usernamePassword(credentialsId: 'dockerhub_cred',
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
