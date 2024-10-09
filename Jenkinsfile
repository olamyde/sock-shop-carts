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
        stage("Workspace cleanup"){
            steps{
                script{
                    cleanWs()
                }
            }
        }
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/olamyde/sock-shop-carts.git', branch: 'main'
            }
        }
    }
    stages {
        stage('Checkout Code') {
            steps {
                sh '''
                    pwd
                '''
            }
        }
    }
            stages {
        stage('Checkout Code') {
            steps {
                sh '''
                    top
                '''
            }
        }

            stages {
        stage('Checkout Code') {
            steps {
                sh '''
                   ls -l
                '''
            }
        }


        stage('Deploy to Kubernetes with Helm') {
            steps {
                script {
                    withCredentials([file(credentialsId: KUBE_CONFIG, variable: 'KUBECONFIG')]) {
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
                cleanWs() // Cleans the workspace after the build
            }
        }
    }
}
