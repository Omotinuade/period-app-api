pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'omotinuade/periodapp'
        DOCKER_USERNAME = 'omotinuade'
        DOCKER_PASSWORD = 'Omotoyinbo1!'
        EC2_USER = 'ec2-user'
        EC2_HOST = '3-85-82-215'
    }
    
    stages {
        stage('Clone') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Omotinuade/period-app-api.git'

            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Docker Test') {
            steps {
                echo "Running "
            }
        }
        
        stage('Login to Docker Hub') {
            steps {
                sh 'docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}'
            }
        }
        
        stage('Docker Test2') {
            steps {
                echo "Running 2"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push ${DOCKER_IMAGE}:latest'
            }
        }
        
        stage('Deploy to EC2') {
            steps {
                sh "ssh -i ~/.ssh/new_age_keypair.pem ${EC2_USER}@${EC2_HOST} 'docker stop periodapp || true'"
                sh "ssh -i ~/.ssh/new_age_keypair.pem ${EC2_USER}@${EC2_HOST} 'docker rm periodapp || true'"
                sh "ssh -i ~/.ssh/new_age_keypair.pem ${EC2_USER}@${EC2_HOST} 'docker pull ${DOCKER_IMAGE}'"
                sh "ssh -i ~/.ssh/new_age_keypair.pem ${EC2_USER}@${EC2_HOST} 'docker run -d --name periodapp -p 8890:8890 ${DOCKER_IMAGE}'"
            }
        }
    }
}
