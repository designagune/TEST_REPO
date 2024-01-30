pipeline {
    agent any

    environment {
        GIT_URL = "https://github.com/designagune/TEST_REPO"
        EC2_IP = "3.90.3.174" // EC2 인스턴스의 IP 주소 test
        EC2_USER = "ec2-user" // EC2 인스턴스의 사용자명
    }

    stages {
        stage('Build and Deploy to Local') {
            when {
                branch 'develop' // develop 브랜치에서만 실행
            }
            steps {
                sh 'docker build -t swerd245/vite-app ./'
                sh 'docker run -d -p 8081:80 --name vite-app swerd245/vite-app'
            }
        }

        stage('Build and Deploy to EC2') {
            when {
                branch 'main' // main 브랜치에서만 실행
            }
            steps {
                echo 'Deploying to EC2 Enviroment'
                script {
                    sshagent (credentials : ['ec2-ssh-key']) {
                        sh "scp -o StrictHostKeyChecking=no ./Dockerfile ec2-user@3.90.3.174:~/"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@3.90.3.174 'docker build -t swerd245/vite-app ./'"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@3.90.3.174 'docker stop vite-app || true && docker rm vite-app || true'"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@3.90.3.174 'docker run -d -p 8081:80 --name vite-app swerd245/vite-app'"
                    }
                }
            }
        }
    }
}
