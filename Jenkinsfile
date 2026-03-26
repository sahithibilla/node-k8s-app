pipeline {
agent any

```
environment {
    IMAGE_NAME = "node-k8s-app"
}

stages {

    stage('Build Docker Image') {
        steps {
            sh 'docker build -t $IMAGE_NAME .'
        }
    }

    stage('Run Container') {
        steps {
            sh '''
            docker stop nodeapp || true
            docker rm nodeapp || true
            docker run -d -p 9090:8080 --name nodeapp $IMAGE_NAME
            '''
        }
    }

    stage('Done') {
        steps {
            echo 'App running at http://localhost:9090'
        }
    }
}
```

}

