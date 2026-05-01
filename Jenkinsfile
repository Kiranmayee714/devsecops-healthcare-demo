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
                bat '''
                chcp 65001
                echo Running Trivy Security Scan...

                trivy image --format table --no-progress --severity HIGH,CRITICAL ^
                --output trivy-report.txt healthcare-devsecops:latest || exit 0

                echo ===============================
                echo Trivy Scan Report:
                echo ===============================
                type trivy-report.txt
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '''
                kubectl apply -f k8s\\deployment.yaml --validate=false
                kubectl apply -f k8s\\service.yaml --validate=false
                '''
            }
        }

        stage('Restart Deployment') {
            steps {
                bat 'kubectl rollout restart deployment healthcare-devsecops-deployment'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '''
                echo Checking Pods...
                kubectl get pods

                echo Checking Services...
                kubectl get svc
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
    }
}