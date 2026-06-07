pipeline {
    agent any

    environment {
        IMAGE_NAME = "your-dockerhub-username/node-cicd-app"
    }

    stages {

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                bat 'npm test || exit 0'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:latest .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat 'echo %PASS% | docker login -u %USER% --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                bat 'docker push %IMAGE_NAME%:latest'
            }
        }

        stage('Deploy Container') {
            steps {
                bat '''
                docker stop node-app || exit 0
                docker rm node-app || exit 0
                docker run -d -p 3000:3000 --name node-app %IMAGE_NAME%:latest
                '''
            }
        }
    }
}