pipeline {
    agent any
    stages {
        stage('Build Backend Image') {
            steps {
                // Change path to 'backend' since CC_LAB-6 folder doesn't exist
                sh 'docker build -t backend-app backend' 
            }
        }
        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker network create app-network || true
                docker rm -f backend1 backend2 || true
                docker run -d --name backend1 --network app-network backend-app
                docker run -d --name backend2 --network app-network backend-app
                '''
            }
        }
        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true
                docker run -d --name nginx-lb --network app-network -p 80:80 nginx
                # Update path here to where your default.conf is (likely nginx/default.conf)
                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }
}
