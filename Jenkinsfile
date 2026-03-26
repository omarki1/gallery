pipeline {
    agent any

    tools {
        nodejs 'NodeJS'
    }

    environment {
        RENDER_DEPLOY_HOOK = 'https://api.render.com/deploy/srv-d72e7g99fqoc73a8onbg?key=lvbpTd5uSS8'
        RENDER_URL = 'https://gallery-ho2d.onrender.com'
        SLACK_CHANNEL = '#omar_IP1'
    }

    triggers {
        githubPush()
    }

    stages {

        stage('Install') {
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'npm test'
            }
            post {
                failure {
                    mail(
                        to: 'kinyanjuiomar60@gmail.com',
                        subject: "Tests Failed - Build #${env.BUILD_NUMBER}",
                        body: "Build #${env.BUILD_NUMBER} failed. Check logs at ${env.BUILD_URL}"
                    )
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to Render...'
                sh 'curl -X POST "$RENDER_DEPLOY_HOOK"'
            }
        }
    }

    post {
        success {
            slackSend(
                channel: "${env.SLACK_CHANNEL}",
                color: 'good',
                message: "✅ Build #${env.BUILD_NUMBER} deployed! Site: ${env.RENDER_URL} | Logs: ${env.BUILD_URL}"
            )
        }
        failure {
            slackSend(
                channel: "${env.SLACK_CHANNEL}",
                color: 'danger',
                message: "❌ Build #${env.BUILD_NUMBER} failed. Logs: ${env.BUILD_URL}"
            )
        }
    }
}
