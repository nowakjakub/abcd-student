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
        stage('Prepare') {
            steps {
                sh 'mkdir -p results/'
            }
        }
        stage('DAST') {
            steps {
                sh '''
                    docker run --name juice-shop -d --rm \
                        -p 3000:300 \
                        bkimminich/juice-shop
                    sleep 5
                '''
                sh '''
                    docker run --name zap \
                    -v /home/novik21e/devSecOps/abcd-student/.zap:/zap/wrk/:rw \
                    -t ghcr.io/zaproxy/zaproxy:stable \
                    bash -c "zap.sh" -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/passive_scan.yaml" \
                '''
            }
        }
    }
    post {
        always {
            sh '''
                
                docker stop zap juice-shop 
                docker rm zap
            '''
        }
    }
}

// docker cp zap:/zap/wrk/reports/zap_html_report.html ${WORKSPACE}/results/zap_html_report.html
//                 docker cp zap:/zap/wrk/reports/zap_xml_report.xml ${WORKSPACE}/results/zap_xml_report.xml