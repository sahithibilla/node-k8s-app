pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "billasahithi/my-k8s-app:${BUILD_NUMBER}"
        DOCKER_USER = credentials('docker-username') 
        DOCKER_PASS = credentials('docker-password')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/sahithibilla/node-k8s-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Docker Login') {
            steps {
                sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Start Minikube (if not running)') {
            steps {
                sh '''
                if ! minikube status | grep -q "apiserver: Running"; then
                    echo "Starting Minikube..."
                    minikube start --driver=docker --memory=2048 --cpus=2
                else
                    echo "Minikube already running"
                fi
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                # Update image tag in deployment.yaml
                sed -i "s|IMAGE_TAG|${BUILD_NUMBER}|g" k8s/deployment.yaml

                # Load image into Minikube
                minikube image load $DOCKER_IMAGE

                # Apply Kubernetes manifests
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Build Failed. Check logs."
        }
    }
}
