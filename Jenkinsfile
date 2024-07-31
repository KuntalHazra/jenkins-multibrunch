pipeline {
    agent any

    environment {
        PYTHON_SCRIPT = 'main.py' // Replace with actual path
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python') {
            when {
                expression {
                    return branch =~ /(feature|hotfix)/ // Adjust branches as needed
                }
            }
            steps {
                script {
                    sh 'python3 --version'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Replace with your actual test commands
                    sh 'python3 -m unittest discover -s tests'
                }
            }
        }

        stage('Code Review and Approval') {
            when {
                expression {
                    return currentBuild.currentResult == 'SUCCESS'
                }
            }
            steps {
                script {
                    // Trigger code review process (e.g., email, chat)
                    // Wait for manual approval
                }
            }
        }

        stage('Merge Test into Dev') {
            when {
                expression {
                    return currentBuild.currentResult == 'SUCCESS' && env.BRANCH_NAME == 'test' && isApproved()
                }
            }
            steps {
                script {
                    sh 'git config user.name "KuntalHazra"'
                    sh 'git config user.email "kuntalhazra16@gmail.com"'
                    sh 'git fetch origin'
                    sh 'git checkout dev'

                    def mergeResult = sh(script: 'git merge origin/test', returnStatus: true)
                    if (mergeResult != 0) {
                        error "Merge conflict occurred. Please resolve manually."
                    }

                    // Push to remote dev branch (replace with your credentials)
                    withCredentials([usernamePassword(credentialsId: 'git-credentials', passwordVariable: 'GITHUB_TOKEN', usernameVariable: 'GITHUB_USER')]) {
                        sh """
                        git remote set-url origin https://${GITHUB_USER}:${GITHUB_TOKEN}@github.com/jenkins-multibranch.git
                        git push origin dev
                        """
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
