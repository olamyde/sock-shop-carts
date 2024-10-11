pipeline {
    agent any
    parameters {
        string(name: 'BUILD_NUMBER', defaultValue: '1.0.1', description: 'Build version number for tagging')
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('s7deji-dockerhub')
        GITHUB_CREDENTIALS = credentials('github-s7deji')
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
        timeout(time: 60, unit: 'MINUTES')
    }
    stages {
        stage('trigger-deployment') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-s7deji', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh '''
                        TAG=${BUILD_NUMBER}
                        
                        # Fetch all branches and checkout the main branch
                        git fetch --all
                        git checkout main

                        # Update key values in `values.yaml` with the TAG
                        yq eval '.image.tag = "'"$TAG"'"' -i values.yaml

                        # Configure Git with GitHub credentials
                        git config --global user.name "olamyde"
                        git config --global user.email "olamyde13@gmail.com"

                        # Commit and push changes
                        git add -A
                        git commit -m "updating APPS to ${BUILD_NUMBER}"
                        git push https://$GIT_USERNAME:$GIT_PASSWORD@github.com/olamyde/sock-shop-carts.git main
                    '''
                }
            }
        }
        
        stage('Helm Install/Upgrade') {
            steps {
                script {
                    // Define the Helm release name and namespace
                    def releaseName = "sock-shop-carts"
                    def namespace = "sock-shop"
                    
                    // Install or upgrade using Helm
                    sh '''
                        helm upgrade --install "${releaseName}" ./sock-shop-carts 
                        # helm upgrade --install "${releaseName}" .
                    '''
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
