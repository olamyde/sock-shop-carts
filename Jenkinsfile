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
                git url: 'https://github.com/your-repo/your-app', branch: 'main'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        def appImage = docker.build("your-repo/your-app:latest")
                        appImage.push()
                    }
                }
            }
        }
        stage('Deploy to Kubernetes with Helm') {
            steps {
                script {
                    def kubeconfig = writeFile file: 'kubeconfig', text: KUBE_CONFIG
                    withEnv(["KUBECONFIG=${kubeconfig}"]) {
                        sh 'helm repo add your-repo https://example.com/charts'
                        sh 'helm repo update'
                        sh '''
                        helm upgrade --install your-release-name your-chart-path \
                        --set image.repository=your-repo/your-app \
                        --set image.tag=latest
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
