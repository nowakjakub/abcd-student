pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'github-pat', url: 'https://github.com/nowakjakub/abcd-student', branch: 'main'
                }
            }
        }
        stage('[ZAP] Baseline passive-scan') {
            steps {
                sh 'mkdir -p results/'
                sh '''
                    docker run --name juice-shop -d -rm \
                        -p 3000:300 \
                        bkimminich/juice-shop
                    sleep 5
                '''
            }
        }
    }
}