pipeline {
    agent any

    environment {
        PYTHON_SCRIPT = 'main.py' // Define the Python script path
        SOURCE_BRANCH = 'test' // The branch with changes
        TARGET_BRANCH = 'dev' // The branch to merge changes into
        GIT_CREDENTIALS_ID = 'git-credentials' // Your Jenkins GitHub credentials ID
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

        stage('Merge Changes') {
            when {
                branch env.SOURCE_BRANCH
            }
            steps {
                script {
                    // Configure Git user
                    sh 'git config user.name "KuntalHazra"'
                    sh 'git config user.email "kuntalhazra16@gmail.com"'

                    // Checkout the target branch
                    sh "git checkout ${env.TARGET_BRANCH}"

                    // Merge changes from source branch into target branch
                    sh "git merge origin/${env.SOURCE_BRANCH}"

                    // Push the merged changes back to the remote repository
                    withCredentials([usernamePassword(credentialsId: env.GIT_CREDENTIALS_ID, passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                            # Use the credentials to set the remote URL and push changes
                            git remote set-url origin https://$GIT_USERNAME:$GIT_PASSWORD@github.com/KuntalHazra/jenkins-multibranch.git
                            git push origin ${TARGET_BRANCH}
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
