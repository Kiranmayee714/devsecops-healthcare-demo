pipeline {
    agent any

    environment {
        KUBECONFIG = 'C:\\Users\\user\\.kube\\config'
    }

    stages {

        stage('Build Docker Image') {
            steps {
                dir('app') {
                    bat 'docker build -t healthcare-devsecops:latest .'
                }
            }
        }

        stage('Security Scan (Trivy)') {
            steps {
                bat 'trivy image healthcare-devsecops:latest'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f k8s\\deployment.yaml --validate=false'
                bat 'kubectl apply -f k8s\\service.yaml --validate=false'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat 'kubectl get pods'
                bat 'kubectl get svc'
            }
        }
    }
}