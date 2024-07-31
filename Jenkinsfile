pipeline {
    agent any

    environment {
        // GitHub credentials ID in Jenkins
        GIT_CREDENTIALS_ID = 'git-credentials'
        // GitHub repository URL
        GIT_REPO_URL = 'https://github.com/KuntalHazra/jenkins-multibranch.git'
    }

    stages {
        stage('Checkout Dev Branch') {
            steps {
                script {
                    // Clone the repository and checkout the dev branch
                    checkout([$class: 'GitSCM', 
                              branches: [[name: '*/dev']],
                              userRemoteConfigs: [[
                                  url: env.GIT_REPO_URL,
                                  credentialsId: env.GIT_CREDENTIALS_ID
                              ]]])
                }
            }
        }
        stage('Merge Test into Dev') {
            steps {
                script {
                    // Attempt to merge the test branch into dev
                    try {
                        sh '''
                        git fetch origin test
                        git merge origin/test
                        '''
                    } catch (Exception e) {
                        echo 'Merge conflict detected!'
                        // Optionally handle merge conflicts
                        // sh 'git merge --abort'  // Uncomment this to abort the merge in case of conflicts
                        error 'Merge failed due to conflicts. Please resolve manually.'
                    }
                }
            }
        }
        stage('Execute Python Script') {
            steps {
                script {
                    // Execute the Python script and handle errors
                    try {
                        sh 'python3 main.py'
                    } catch (Exception e) {
                        error 'Python script execution failed!'
                    }
                }
            }
        }
        stage('Push Changes to Dev') {
            steps {
                script {
                    // Push changes back to the dev branch
                    try {
                        sh '''
                        git push origin dev
                        '''
                    } catch (Exception e) {
                        error 'Failed to push changes to the dev branch.'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded! Changes merged and script executed.'
        }
        failure {
            echo 'Pipeline failed! Check logs for details.'
        }
    }
}
