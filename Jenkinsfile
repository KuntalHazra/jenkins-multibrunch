pipeline {
    agent any

    environment {
        PYTHON_SCRIPT = 'main.py' // Define the Python script path
        GIT_URL = 'https://github.com/KuntalHazra/jenkins-multibrunch.git'
        GIT_CREDENTIALS = 'git-credentials' // Make sure this credential ID exists in Jenkins
        GIT_REPO = 'github.com/KuntalHazra/jenkins-multibrunch.git' // Repository URL for push
    }

    stages {
        stage('Initialize') {
            steps {
                // Clone the repository using credentials
                git url: env.GIT_URL, credentialsId: env.GIT_CREDENTIALS
            }
        }

        stage('Checkout') {
            steps {
                // Checkout the code from the current branch
                checkout scm
            }
        }

        stage('Setup Python') {
            steps {
                script {
                    // Print the branch name being built
                    echo "Building branch: ${env.BRANCH_NAME}"

                    // Ensure Python is installed
                    sh 'python3 --version'
                }
            }
        }

        stage('Run Python Script') {
            steps {
                script {
                    // Run the Python script
                    sh "python3 ${env.PYTHON_SCRIPT}"
                }
            }
        }

        stage('Merge Test into Dev') {
            when {
                // Only execute this stage if the current branch is test
                branch 'test'
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

                    // Merge changes from test into dev
                    sh 'git merge origin/test'

                    // Push the changes to the remote dev branch using stored credentials
                    withCredentials([usernamePassword(credentialsId: env.GIT_CREDENTIALS, passwordVariable: 'GITHUB_TOKEN', usernameVariable: 'GITHUB_USER')]) {
                        sh "git push https://${env.GITHUB_USER}:${env.GITHUB_TOKEN}@${env.GIT_REPO} dev"
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
