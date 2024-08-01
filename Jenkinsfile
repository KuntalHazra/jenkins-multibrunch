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

        stage('Setup Python') {
            steps {
                script {
                    echo "Building branch: ${env.BRANCH_NAME}" // Print the branch name being built
                    sh 'python3 --version' // Ensure Python is installed
                }
            }
        }

        stage('Run Python Script') {
            steps {
                script {
                    sh "python3 ${env.PYTHON_SCRIPT}" // Run the Python script
                }
            }
        }

        stage('Merge Test into Dev') {
            when {
                branch 'test' // Only execute this stage if the current branch is 'test'
            }
            steps {
                script {
                    echo "Merging changes from test into dev"

                    // Configure git user for the merge operation
                    sh 'git config user.name "KuntalHazra"'
                    sh 'git config user.email "kuntalhazra16@gmail.com"'

                    // Fetch the latest changes
                    sh 'git fetch origin'

                    // Checkout the dev branch
                    sh 'git checkout dev'

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
                            git remote set-url origin https://${credentialsId: env.GIT_CREDENTIALS_ID}:${usernameVariable: 'GIT_USER'}:${passwordVariable: 'GIT_TOKEN'}@github.com/KuntalHazra/jenkins-multibranch.git
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