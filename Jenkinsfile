pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('del-docker-hub-auth')
    }
    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/olamyde/sock-shop-carts.git', branch: 'main'
            }
        }
        stage('Set Up Minikube Kubeconfig') {
            steps {
                script {
                    // Use the local Minikube kubeconfig file
                    env.KUBECONFIG = "${HOME}/.kube/config"
                }
            }
        }
        stage('Deploy to Minikube with Helm') {
            steps {
                script {
                    sh '''
                    helm upgrade --install sock-shop-carts ./sock-shop-carts/
                    '''
                }
            }
        }
    }
    post {
        always {
            cleanWs() // Cleans the workspace after the build
        }
    }
}
