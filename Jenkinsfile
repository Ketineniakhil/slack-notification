pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/Ketineniakhil/slack-notification.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Application') {
            steps {
                sh 'npm run build || echo "No build step needed"'
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no -i /root/.ssh/default-ecc.pem ubuntu@107.21.85.24 "mkdir -p /var/www/app"'
                sh 'rsync -avz -e "ssh -o StrictHostKeyChecking=no -i /root/.ssh/default-ecc.pem" --exclude node_modules . ubuntu@107.21.85.24:/var/www/app'
                sh 'ssh -o StrictHostKeyChecking=no -i /root/.ssh/default-ecc.pem ubuntu@107.21.85.24 "cd /var/www/app && npm install"'
                sh 'ssh -o StrictHostKeyChecking=no -i /root/.ssh/default-ecc.pem ubuntu@107.21.85.24 "pm2 restart slack-app || pm2 start /var/www/app/index.js --name slack-app"'
            }
        }
    }

    post {
        always {
            sh 'rm -rf ./build'
        }
    }
}
