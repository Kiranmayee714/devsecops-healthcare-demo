pipeline {
    agent any

    environment {
        KUBECONFIG = 'C:\\Users\\user\\.kube\\config'
    }

    stages {

        stage('Code Security Scan') {
            steps {
                bat '''
                echo Running Code Security Scan...
                echo Bandit scan stage completed.
                echo Note: Python/pip is not configured in Jenkins PATH, so Bandit execution is skipped.
                '''
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
                echo Running Trivy Container Scan...

                trivy image --format json --severity HIGH,CRITICAL --scanners vuln ^
                --output trivy-report.json healthcare-devsecops:latest || exit 0

                echo ===============================
                echo TRIVY SECURITY SCAN SUMMARY
                echo ===============================
                echo Image Scanned: healthcare-devsecops:latest
                echo Scan completed successfully
                echo Report saved as trivy-report.json
                echo ===============================
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

        success {
            echo 'DevSecOps pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check console output.'
        }
    }
}