pipeline {
    agent any

    environment{
      DOCKER_USER = "nityavadoni"
      NODE_IMAGE = "${DOCKER_USER}/node-app"
      NGINX_IMAGE = "${DOCKER_USER}/nginx"
      VM_IP = "100.48.62.55"
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

        stage('Deploy to Target VM') {
               steps {
                   sshagent(['ec2-ssh-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@$VM_IP << 

                    docker pull $NODE_IMAGE
                    docker pull $NGINX_IMAGE

                    docker network create net-app 

                    docker run -d -p 3000:3000 --name Nodejs --network net-app $NODE_IMAGE
                    docker run -d -p 80:80 --name Nginx --network net-app $NGINX_IMAGE

                    docker stop nodeapp nginx || true
                    docker rm nodeapp nginx || true
                 """
                   }
               }
        }
    }
}
