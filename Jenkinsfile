pipeline {
    agent any

    environment {
        DOCKER_USERNAME = credentials('docker_username')
        DOCKER_PASSWORD = credentials('docker_password')
        DOCKER_IMAGE = 'omotinuade/periodapp'
        DOCKER_CREDENTIALS = credentials('docker')
        BUILD_NUMBER = 'latest'
        EC2_USER = 'ec2-user'
        EC2_HOST = '54.198.95.9'
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id') // Jenkins secret text credential
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key') // Jenkins secret text credential
        SSH_KEY_PATH = credentials('ssh-private-key') // Jenkins SSH Username with private key credential
        SECRET_DB_URL = credentials('secret-db-url') // Jenkins secret text credential
        SECRET_DB_USERNAME = credentials('secret-db-username') // Jenkins secret text credential
        SECRET_DB_PASSWORD = credentials('secret-db-password') // Jenkins secret text credential
        SECRET_SECURITY_JWTEXPTIME = credentials('secret-security-jwtextime') // Jenkins secret text credential
        SECRET_SECURITY_JWTSECRETKEY = credentials('secret-security-jwtsecretkey') // Jenkins secret text credential
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
       
        stage('Build and tag Docker image') {
            steps { 
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }
        
        stage('Log in to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(credentialsId: "${DOCKER_CREDENTIALS}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')
                ]) { 
                sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                }
            }
        }
        
        stage('Push Docker image to Docker Hub') {
            steps {
                sh "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
            }
        }
        
        stage('Deploy to EC2') {
            steps {
                sh "ssh -i ${SSH_KEY_PATH} ${EC2_USER}@${EC2_HOST} 'docker stop periodapp || true'"
                sh "ssh -i ${SSH_KEY_PATH} ${EC2_USER}@${EC2_HOST} 'docker rm periodapp || true'"
                sh "ssh -i ${SSH_KEY_PATH} ${EC2_USER}@${EC2_HOST} 'docker pull ${DOCKER_IMAGE}'"
                sh "ssh -i ${SSH_KEY_PATH} ${EC2_USER}@${EC2_HOST} 'docker run -d \
                    -e secret_db_url=\"${SECRET_DB_URL}\" \
                    -e secret_db_username=\"${SECRET_DB_USERNAME}\" \
                    -e secret_db_password=\"${SECRET_DB_PASSWORD}\" \
                    -e secret_security_jwtExpTime=\"${SECRET_SECURITY_JWTEXPTIME}\" \
                    -e secret_security_jwtSecretKey=\"${SECRET_SECURITY_JWTSECRETKEY}\" \
                    --name periodapp \
                    -p 8891:8891 \
                    ${DOCKER_IMAGE}'"
            }
        }
    }
}
