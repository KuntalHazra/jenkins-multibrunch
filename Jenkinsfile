pipeline {
    agent any

    environment {
        PYTHON_SCRIPT = 'main.py' // Define the Python script path
    }

    stages {
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

                    // Push the changes to the remote dev branch
                    sh 'git push origin dev'
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