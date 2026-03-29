pipeline {
    agent any

    environment {
        REMOTE_HOST = "docker@10.20.30.93" 
        REPO_URL = "https://github.com/sudhanshu-cpu/wordpress-project.git"
        APP_DIR = "~/wordpress" // directory on Docker host to deploy
    }

    stages {
        stage('Checkout') {
            steps {
                git url: "${REPO_URL}", branch: 'main'
            }
        }

        stage('Deploy to Docker Host') {
            steps {
                sshagent(['my-ssh-credentials-id']) { // ID of the SSH key in Jenkins 
                    // Create remote directory
                    sh "ssh -o StrictHostKeyChecking=no ${REMOTE_HOST} 'mkdir -p ${APP_DIR}'"

                    // Copy files to remote host
                    sh "scp -r * ${REMOTE_HOST}:${APP_DIR}/"

                    // Run docker-compose on remote host
                    sh "ssh ${REMOTE_HOST} 'cd ${APP_DIR} && docker compose down && docker compose up -d'"
                }
            }
        }
    }

    post {
        failure {
            echo "Deployment failed. Check Jenkins logs."
        }
    }
}
