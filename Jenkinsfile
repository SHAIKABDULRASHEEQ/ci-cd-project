pipeline { 
    agent any

    environment {
        IMAGE_NAME = "shaikabdulrasheeq/cicd-demo"
        DOCKER_CREDS = "dockerhub-creds"
    }

    stages {

        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Build Image") {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage("Docker Login") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: DOCKER_CREDS,
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage("Push Image") {
            steps {
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }

        /* ✅ FIXED DEPLOY STAGE */
        stage("Deploy to Kubernetes") {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                kubectl rollout status deployment/cicd-demo
                '''
            }
        }
    }
}
