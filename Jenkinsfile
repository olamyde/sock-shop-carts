pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('del-docker-hub-auth')
        KUBE_CONFIG = credentials('kubeconfig')
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
        timeout(time: 60, unit: 'MINUTES')
        timestamps()
    }
    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/olamyde/sock-shop-carts.git', branch: 'main'
            }
        }
        stage('Deploy to Kubernetes with Helm') {
            steps {
                script {
                    withEnv(["KUBECONFIG=${KUBE_CONFIG}"]) {
                        sh '''
                        helm upgrade --install sock-shop-carts /home/olamide/sock-shop/sock-shop-carts
                        '''
                    }
                }
            }
        }
    }
    post {
        always {
            // Wrap cleanWs() in a node block to ensure it has the required context
            node {
                cleanWs() // Cleans the workspace after the build
            }
        }
    }
}
