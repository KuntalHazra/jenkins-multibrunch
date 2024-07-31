pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/KuntalHazra/jenkins-multibrunch.git'
        GIT_CREDENTIALS_ID = 'git-credentials'
    }

    stages {
        stage('Checkout test') {
            steps {
                checkout([$class: 'GitSCM', 
                    branches: [[name: '*/test']], 
                    userRemoteConfigs: [[url: "${env.GIT_REPO}", credentialsId: "${env.GIT_CREDENTIALS_ID}"]]
                ])
            }
        }

        stage('Merge test to dev') {
            steps {
                script {
                    sh '''
                    git config user.name "KuntalHazra"
                    git config user.email "kuntalhazra16@gmail.com"

                    git fetch origin

                    git checkout test
                    git pull origin test

                    git checkout dev
                    git pull origin dev

                    git merge test

                    git push origin dev
                    '''
                }
            }
        }
    }
}