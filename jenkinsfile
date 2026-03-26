pipeline {
agent any

```
environment {
    IMAGE_NAME = "node-k8s-app"
}

stages {

    stage('Clone') {
        steps {
            git 'https://github.com/sahithibilla/node-k8s-app.git'
        }
    }

    stage('Build Docker Image') {
        steps {
            script {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }
    }

    stage('Run Container') {
        steps {
            script {
                sh '''
                docker stop nodeapp || true
                docker rm nodeapp || true
                docker run -d -p 9090:8080 --name nodeapp $IMAGE_NAME
                '''
            }
        }
    }

    stage('Verify') {
        steps {
            echo 'App deployed at http://localhost:9090'
        }
    }
}
```

}

