pipeline {
    agent { label "Jenkins-Agent" }

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: '', description: 'Docker image tag to deploy, e.g. 1.0.0-20')
    }

    environment {
        APP_NAME = "register-app-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/ixsnehith/gitops-register-app.git'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                    echo '--- BEFORE replacement ---'
                    cat deployment.yaml
                    sed -i 's|\\\${IMAGE_TAG}|${IMAGE_TAG}|g' deployment.yaml
                    echo '--- AFTER replacement ---'
                    cat deployment.yaml
                    # Optional: fail the build if replacement didn\'t occur
                    if grep '\\\${IMAGE_TAG}' deployment.yaml; then
                        echo 'ERROR: IMAGE_TAG was not replaced!'
                        exit 1
                    fi
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git config --global user.name "ixsnehith"
                    git config --global user.email "snehith.boreddy@innovatechs.com"
                    git add deployment.yaml
                    git commit -m "Updated Deployment Manifest for tag ${IMAGE_TAG}" || echo "Nothing to commit"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/ixsnehith/gitops-register-app.git main"
                }
            }
        }
    }
}
