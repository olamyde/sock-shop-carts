pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('del-docker-hub-auth')
        KUBE_CONFIG = credentials('kube-config') 
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
                        sh '''
                        cd ${WORKSPACE}/sock-shop-carts
                        helm upgrade --install sock-shop-carts .
                        '''
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs() // Cleans the workspace after the build
        }
    }

