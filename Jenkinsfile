pipeline {
    agent any
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

                        git add -A
                        git commit -m "updating APPS to ${BUILD_NUMBER}"
                        
                        # Push changes to the main branch
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
                        export KUBECONFIG=/home/automation/.kube/config
                        helm upgrade --install "${releaseName}" "./sock-shop-carts" \\
                            --namespace "${namespace}" \\
                            -f values.yaml \\
                            --set image.tag=${BUILD_NUMBER}
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
