pipeline {
    agent any
    environment {
        APP_NAME = 'web-deployment'
        K8S_CREDENTIALS = 'kubeconfig'
    }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Lint YAML') {
            steps {
                echo 'Vérification syntaxique du manifest...'
                withKubeConfig([credentialsId: "${K8S_CREDENTIALS}"]) {
                    sh 'kubectl apply --dry-run=server -f deployment.yaml'
                    sh 'kubectl apply --dry-run=server -f svc.yaml'
                }
            }
        }
        stage('Deploy to K8s') {
            steps {
                withKubeConfig([credentialsId: "${K8S_CREDENTIALS}"]) {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f svc.yaml'
                }
            }
        }
        stage('Verify Health') {
            steps {
                withKubeConfig([credentialsId: "${K8S_CREDENTIALS}"]) {
                    sh "kubectl rollout status deployment/${APP_NAME}"
                    sh "kubectl get pods -l app=${APP_NAME} -o wide"
                }
            }
        }
    }
}
