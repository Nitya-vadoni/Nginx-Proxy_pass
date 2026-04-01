pipeline {
    agent any

    environment{
      DOCKER_USER = "nityavadoni"
      NODE_IMAGE = "${DOCKER_USER}/node-app"
      NGINX_IMAGE = "${DOCKER_USER}/nginx"
      VM_IP = "100.53.219.77"
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
            sh '''
            ssh -o StrictHostKeyChecking=no ubuntu@$VM_IP << EOF

            echo "Running on: $(hostname)"

            docker pull nityavadoni/node-app:latest
            docker pull nityavadoni/nginx:latest

            docker stop Nodejs || true
            docker rm Nodejs || true

            docker stop Nginx || true
            docker rm Nginx || true

            docker network create net-app || true

            docker run -d -p 3000:3000 --name Nodejs --network net-app nityavadoni/node-app:latest
            docker run -d -p 80:80 --name Nginx --network net-app nityavadoni/nginx:latest

            EOF
            '''
        }
    }
}
}
}
