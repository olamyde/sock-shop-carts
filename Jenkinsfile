pipeline {
    agent any
    environment {
        KUBE_CONFIG = 'kubeconfig'
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
        timeout(time: 60, unit: 'MINUTES')
        timestamps()
    }
    stages {
        stage("Workspace Cleanup") {
            steps {
                cleanWs()
            }
        }
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/olamyde/sock-shop-carts.git', branch: 'main'
            }
        }
        stage('Print Working Directory') {
            steps {
                sh 'pwd'
            }
        }
        stage('Run Top Command') {
            steps {
                sh 'top -b -n 1' // Using batch mode for 'top' to prevent it from hanging
            }
        }
        stage('List Directory Contents') {
            steps {
                sh 'ls -l'
            }
        }
        stage('Deploy to Kubernetes with Helm') {
            steps {
                withCredentials([file(credentialsId: KUBE_CONFIG, variable: 'KUBECONFIG')]) {
                    sh '''
                    helm upgrade --install sock-shop-carts /home/olamide/sock-shop/sock-shop-carts
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
