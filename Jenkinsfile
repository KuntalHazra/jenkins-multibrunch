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
    }

    post {
        always {
            script {
                echo "Build complete for branch: ${env.BRANCH_NAME}"
            }
        }
    }
}
