pipeline {

    agent any

    environment {
        IMAGE_NAME = "ruthwik45/website-cicd-project:latest"
        APP_SERVER = "13.218.250.117"
    }

    stages {

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS')
                ]) {

                sh '''
                echo "$PASS" | docker login \
                -u "$USER" \
                --password-stdin
                '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no ec2-user@$APP_SERVER "

                docker pull $IMAGE_NAME

                docker stop website || true

                docker rm website || true

                docker run -d \
                --name website \
                -p 80:80 \
                $IMAGE_NAME
                "
                '''
            }
        }
    }
}
