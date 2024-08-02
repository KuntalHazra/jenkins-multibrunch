pipeline {
    agent any

    environment {
        GIT_CREDENTIALS_ID = 'git-credentials'  // The ID of the credentials you added in Jenkins
    }

    stages {
        stage('Checkout Test Branch') {
            steps {
                // Checkout the test branch
                git branch: 'test', url: 'https://github.com/KuntalHazra/jenkins-multibrunch.git'
            }
        }
        stage('Merge with Dev Branch') {
            steps {
                script {
                    // Merge changes from test branch to dev branch
                    sh 'git checkout dev'
                    sh 'git merge test'
                    
                    // Push changes to dev branch
                    withCredentials([usernamePassword(credentialsId: env.GIT_CREDENTIALS_ID, usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        sh 'https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/KuntalHazra/jenkins-multibrunch.git dev'
                    }
                }
            }
        }
    }
}