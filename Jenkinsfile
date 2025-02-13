pipeline {
    agent any
    environment {
        IMAGE_NAME = 'aakki2503/akshatnewimg6july'
        IMAGE_TAG = 'v1'
        CONTAINER_NAME = 'My-first-containe2211'
        SERVER_IP = '172.31.27.38'
    }
    stages {
        stage('Git Clone') {
            steps {
                script {
                    sh 'rm -rf php-project || true'
                }
                git url: 'https://github.com/dhanshettiaakash/php-project/', branch: "master"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                    sh 'docker images'
                }
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-pwd', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sshagent(['sshkeypair']) {
                        sh """
                            ssh -o StrictHostKeyChecking=no ubuntu@${SERVER_IP} '
                            docker ps -q --filter "name=${CONTAINER_NAME}" | grep -q . && docker rm -f ${CONTAINER_NAME} || true
                            docker pull ${IMAGE_NAME}:${IMAGE_TAG}
                            docker run -itd --name ${CONTAINER_NAME} -p 8083:80 ${IMAGE_NAME}:${IMAGE_TAG}
                            '
                        """
                    }
                }
            }
        }
    }
}
