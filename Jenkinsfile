def notifyWebex(String status) {
    withCredentials([
        string(credentialsId: 'webex-bot-token', variable: 'WEBEX_TOKEN'),
        string(credentialsId: 'webex-room-id', variable: 'WEBEX_ROOM')
    ]) {
        sh """
            curl -s -X POST https://webexapis.com/v1/messages \
              -H "Authorization: Bearer \$WEBEX_TOKEN" \
              -H "Content-Type: application/json" \
              -d '{"roomId": "'"\$WEBEX_ROOM"'", "markdown": "**${status}**: ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${env.BUILD_URL}"}'
        """
    }
}

pipeline {
    agent any
    triggers { githubPush() }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Setup') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }
        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    python -m pytest -v
                '''
            }
        }
    }

    post {
        success { script { notifyWebex('BUILD SUCCESS') } }
        failure { script { notifyWebex('BUILD FAILED') } }
    }
}
