pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('del-docker-hub-auth')
        GITHUB_CREDENTIALS = credentials('github-s7deji')
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
        timeout(time: 60, unit: 'MINUTES')
        timestamps()
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

                        git add -A
                        git commit -m "updating APPS  to ${BUILD_NUMBER}"
                        
                        # Push changes to the main branch
                        git push https://$GIT_USERNAME:$GIT_PASSWORD@github.com/olamyde/sock-shop-carts.git main
                    '''
                }
            }
        }
    }
    post {
        success {
            slackSend(channel: '#development-alerts', color: 'good', message: "SUCCESSFUL: Application Eric-do-it-yourself-assets  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
        unstable {
            slackSend(channel: '#development-alerts', color: 'warning', message: "UNSTABLE: Application Eric-do-it-yourself-assets  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
        failure {
            slackSend(channel: '#development-alerts', color: '#FF0000', message: "FAILURE: Application Eric-do-it-yourself-assets Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
        cleanup {
            deleteDir()
        }
    }
}
