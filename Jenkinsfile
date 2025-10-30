pipeline {
    agent { label "Jenkins-Agent" }

    environment {
        APP_NAME = "flask-image-editor"
        DOCKER_USER = "jay0604"
        BASE_VERSION = "1.0.0"
        IMAGE_TAG = "1.0.0-15"
    }

    stages {
        stage("Checkout Deployment Repo") {
            steps {
                echo "Checking out deployment repo..."
                git branch: 'main', credentialsId: 'GitHub', url: 'https://github.com/JaydeepDebnath/flask-image-editor-deploy.git'
            }
        }

        stage("Update Image Tag in deployment.yaml") {
            steps {
                echo "Updating image tag..."
                sh '''
                sed -i "s|image:.*|image: jay0604/flask-image-editor:${IMAGE_TAG}|" deployment.yaml
                echo "Updated deployment.yaml:"
                cat deployment.yaml
                '''
            }
        }

        stage("Commit & Push Changes") {
            steps {
                echo "Pushing updated manifest to GitHub..."
                withCredentials([usernamePassword(credentialsId: 'GitHub', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                    sh '''
                    git config --global user.name "JaydeepDebnath"
                    git config --global user.email "mejaydeepdebnath@gmail.com"
                    git add deployment.yaml
                    git commit -m "Update image tag to ${IMAGE_TAG}"
                    git push https://${GIT_USER}:${GIT_PASS}@github.com/JaydeepDebnath/flask-image-editor-deploy.git main
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Successfully updated deployment.yaml to ${IMAGE_TAG}. ArgoCD will auto-sync."
        }
        failure {
            echo "Failed to update deployment.yaml. Check logs."
        }
    }
}
