pipeline {
    agent any

    environment {
        // GitHub credentials ID in Jenkins
        GIT_CREDENTIALS_ID = 'git-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the 'dev' branch
                git branch: 'dev', 
                    credentialsId: env.GIT_CREDENTIALS_ID,
                    url: 'https://github.com/KuntalHazra/jenkins-multibranch.git'
            }
        }
        stage('Merge Test into Dev') {
            steps {
                script {
                    // Merge the 'test' branch into the current branch (dev)
                    sh '''
                    git config --global user.email "kuntalhazra16@gmail.com"
                    git config --global user.name "KuntalHazra"
                    git fetch origin test
                    git merge origin/test || true
                    '''
                }
            }
        }
        stage('Execute Python Script') {
            steps {
                script {
                    // Execute the Python script
                    sh 'python3 main.py'
                }
            }
        }
        stage('Push Changes') {
            steps {
                script {
                    // Push the merged changes back to the dev branch
                    sh '''
                    git push origin dev || true
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
