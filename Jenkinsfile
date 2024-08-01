pipeline {
    agent any

    environment {
        PYTHON_SCRIPT = 'main.py' // Define the Python script path
        GIT_CREDENTIALS_ID = 'git-credentials' // Jenkins credentials ID
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // Checkout the code from the current branch
            }
        }

        stage('Check for Changes in main.py') {
            steps {
                script {
                    // Check if main.py has changed
                    def changes = sh(script: 'git diff --name-only HEAD~1 HEAD', returnStdout: true).trim()
                    if (changes.contains('main.py')) {
                        echo "Changes detected in main.py. Proceeding with merge."
                        currentBuild.result = 'SUCCESS'
                    } else {
                        echo "No changes in main.py. Skipping merge."
                        currentBuild.result = 'SUCCESS'
                        return
                    }
                }
            }
        }

        stage('Merge Test into Dev') {
            steps {
                script {
                    echo "Merging changes from test into dev"

                    // Configure git user for the merge operation
                    sh 'git config user.name "KuntalHazra"'
                    sh 'git config user.email "kuntalhazra16@gmail.com"'

                    // Fetch the latest changes from the remote repository
                    sh 'git fetch origin'

                    // Checkout the dev branch (create it if it doesn't exist)
                    sh 'git checkout -b dev origin/dev || git checkout dev'

                    // Attempt to merge changes from test into dev
                    def mergeResult = sh(script: 'git merge origin/test', returnStatus: true)

                    if (mergeResult != 0) {
                        // Merge conflict detected
                        echo "Merge conflict occurred. Please resolve the conflict manually."
                        error "Merge conflict occurred. Manual intervention required."
                    }

                    // Push the changes to the remote dev branch using credentials
                    withCredentials([usernamePassword(credentialsId: env.GIT_CREDENTIALS_ID, passwordVariable: 'GIT_TOKEN', usernameVariable: 'GIT_USER')]) {
                        sh '''
                            git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/KuntalHazra/jenkins-multibranch.git
                            git push origin dev
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                echo "Build complete for branch: ${env.BRANCH_NAME}"
            }
        }
    }
}
