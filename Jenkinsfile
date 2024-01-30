pipeline {
    agent any

    environment {
        GIT_URL = "https://github.com/designagune/TEST_REPO"
    }
    stages {
        stage('Frontend Build') {
            steps {
                sh 'docker build -t swerd245/vite-app ./'
            }
        }

        stage('Frontend Deploy') {
            steps {
                script {
                    // 80번 포트를 사용 중인 컨테이너 확인 및 중지
                    def isPortInUse = sh(script: "docker ps -q --filter 'port=8081'", returnStatus: true)
                    if (isPortInUse == 0) {
                        sh "docker ps -q --filter 'port=8181' | xargs -r docker stop"
                    }

                    // 기존 vite-app 컨테이너 확인 및 중지
                    def isRunning = sh(script: 'docker ps -a -q --filter name=vite-app | grep -q .', returnStatus: true)
                    if (isRunning == 0) {
                        sh 'docker stop vite-app && docker rm vite-app'
                    }
                }
                sh 'docker run -d -p 8081:80 --name vite-app swerd245/vite-app'
            }
        }

        stage('Frontend Finish') {
            steps {
                    // 중지된 컨테이너를 삭제
                    sh 'docker ps -a -q --filter status=exited | xargs -r docker rm'
                    // dangling 이미지 강제 삭제
                    sh 'docker images -qf dangling=true | xargs -r docker rmi -f'
            }
        }
    }
}


