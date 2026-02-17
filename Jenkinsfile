pipeline {
    agent any

    stages {
        stage('Build Backend Image') {
            steps {
                sh '''
                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true
                docker run -d --name backend1 backend-app
                docker run -d --name backend2 backend-app
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true
                docker run -d -p 80:80 --name nginx-lb \
                --link backend1 --link backend2 \
                nginx
                '''
            }
        }
    }
}
