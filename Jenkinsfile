pipeline {
    agent any

    parameters {
        string(name: 'PORT', defaultValue: '8082', description: 'Port for NGINX Load Balancer')
    }

    stages {

        stage('Build Backend Image') {
            steps {
                sh '''
                echo "Building backend Docker image..."
                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                echo "Deploying backend containers..."
                docker rm -f backend1 backend2 || true

                docker run -d --name backend1 backend-app
                docker run -d --name backend2 backend-app
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                echo "Starting NGINX Load Balancer..."
                docker rm -f nginx-lb || true

                WORKSPACE_DIR=$(pwd)

                docker run -d -p ${PORT}:80 \
                  --name nginx-lb \
                  --link backend1 \
                  --link backend2 \
                  -v $WORKSPACE_DIR/nginx/nginx.conf:/etc/nginx/nginx.conf:ro \
                  nginx
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully! Load balancer running on http://localhost:${PORT}"
        }
        failure {
            echo "Pipeline failed. Check console logs for errors."
        }
    }
}
