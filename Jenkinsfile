pipeline {
    agent { label "Jenkins-Agent" }
    environment {
        APP_NAME = "register-app-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps { cleanWs() }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/ixsnehith/gitops-register-app.git'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   echo '--- Before replacement ---'
                   cat deployment.yaml
                   # Replace only the \${IMAGE_TAG} placeholder with the actual tag
                   sed -i 's|\\${IMAGE_TAG}|${IMAGE_TAG}|g' deployment.yaml
                   echo '--- After replacement ---'
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                   git config --global user.name "ixsnehith"
                   git config --global user.email "snehith.boreddy@innovatechs.com"
                   git add deployment.yaml
                   git commit -m "Updated Deployment Manifest" || echo "Nothing to commit"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/ixsnehith/gitops-register-app.git main"
                }
            }
        }
    }
}
