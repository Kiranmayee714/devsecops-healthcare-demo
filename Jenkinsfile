pipeline {
    agent any

    environment {
        KUBECONFIG = "C:\\Users\\user\\.kube\\config"
        PYTHON_EXE = "C:\\Python311\\python.exe"
    }

    stages {

        stage('Code Security Scan') {
            steps {
                bat "\"%PYTHON_EXE%\" --version"
                bat "\"%PYTHON_EXE%\" -m pip --version"
                bat "\"%PYTHON_EXE%\" -m pip install bandit"
                bat "\"%PYTHON_EXE%\" -m bandit -r app || exit 0"
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('app') {
                    bat 'docker build -t healthcare-devsecops:latest .'
                }
            }
        }

        stage('Container Security Scan') {
            steps {
                bat 'chcp 65001'
                bat 'trivy image --format table healthcare-devsecops:latest || exit 0'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f k8s\\deployment.yaml --validate=false'
                bat 'kubectl apply -f k8s\\service.yaml --validate=false'
            }
        }

        stage('Restart Deployment') {
            steps {
                bat 'kubectl rollout restart deployment healthcare-devsecops-deployment'
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