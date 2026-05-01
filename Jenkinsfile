pipeline {
    agent any

    environment {
        KUBECONFIG = 'C:\\Users\\user\\.kube\\config'
    }

    stages {

        stage('Code Security Scan') {
            steps {
                bat 'pip install bandit'
                bat 'bandit -r app/ || exit 0'
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
                bat '''
                echo Running Trivy Scan...
                trivy image --format json --severity HIGH,CRITICAL --scanners vuln ^
                --output trivy-report.json healthcare-devsecops:latest || exit 0
                echo Scan Completed
                '''
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