pipeline {
    agent any  

    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-creds'
        DOCKER_IMAGE_NAME = 'nadinc/mvn:latest'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/nadin-c/MVNM_project.git', branch: 'main'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE_NAME .'
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $DOCKER_IMAGE_NAME'
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker run -d -p 30002:80 $DOCKER_IMAGE_NAME'
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
