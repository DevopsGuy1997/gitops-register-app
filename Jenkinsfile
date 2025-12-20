pipeline {
    agent { label "Jenkins-Agent" }
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
                   git branch: 'main', credentialsId: 'github', url: 'https://github.com/DevopsGuy1997/gitops-register-app.git'
               }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
    steps {
        // 'github' should match the ID of your credential in Jenkins
        withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
            sh '''
                # 1. Setup local identity (required for committing)
                git config --global user.name "DevopsGuy1997"
                git config --global user.email "rakeshmothkur@gmail.com"

                # 2. Stage the file
                git add deployment.yaml

                # 3. Only commit if there are changes (avoids exit code 1)
                if ! git diff-index --quiet HEAD; then
                    git commit -m "Updated Deployment Manifest to version ${IMAGE_TAG}"
                    
                    # 4. Push using HTTPS with the token injected for authentication
                    # This replaces the git@github.com SSH line that failed
                    git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/DevopsGuy1997/gitops-register-app.git main
                else
                    echo "No changes detected. Skipping push."
                fi
            '''
           }
        }
      }
   }
}
