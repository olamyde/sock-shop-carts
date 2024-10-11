pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('del-docker-hub-auth')
        GITHUB_CREDENTIALS = credentials('s7deji-github2') // Add GitHub credentials here
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
                withCredentials([usernamePassword(credentialsId: 's7deji-github2', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    sh '''
                        TAG=${BUILD_NUMBER}

                        # Update key values in `values.yaml` with the TAG
                        yq eval '.image.tag = "'"$TAG"'"' -i values.yaml

                        # Configure Git with GitHub credentials
                        git config --global user.name "olamyde"
                        git config --global user.email "olamyde13@gmail.com"

                        git add -A
                        if git diff-index --quiet HEAD; then
                            echo "No changes to commit"
                        else
                            git commit -m "updating APPS  to ${BUILD_NUMBER}"
                            git push https://github.com/olamyde/sock-shop-carts.git main
                        fi
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
