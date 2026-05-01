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
        powershell '''
        [Console]::OutputEncoding = [System.Text.Encoding]::UTF8
        $OutputEncoding = [System.Text.Encoding]::UTF8
        chcp 65001

        trivy image --format table --no-progress --severity HIGH,CRITICAL healthcare-devsecops:latest
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