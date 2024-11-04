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
                // sh '''
                //     docker run --name juice-shop -d --rm \
                //         -p 3000:3000 \
                //         bkimminich/juice-shop
                //     sleep 5
                // '''
                // sh '''
                //     docker run --name zap \
                //     -v /home/novik21e/devSecOps/abcd-student/.zap:/zap/wrk/:rw \
                //     -t ghcr.io/zaproxy/zaproxy:stable \
                //     bash -c "zap.sh -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/passive.yaml" \
                //     || true
                // '''
            }
            post {
                always {
                    // sh '''
                    //     docker cp zap:/zap/wrk/reports/zap_html_report.html ${WORKSPACE}/results/zap_html_report.html
                    //     docker cp zap:/zap/wrk/reports/zap_xml_report.xml ${WORKSPACE}/results/zap_xml_report.xml
                    //     docker stop zap juice-shop
                    //     docker rm zap
                    // '''
                }
            }
        }
        stage('SCA scan') {
            steps {
                // sh 'osv-scanner scan -L package-lock.json -f json --output results/sca-osv-scanner.json || true'
            }
        }
        stage('TruffleHog Scan') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    // sh 'trufflehog git file://. --branch main --json --fail > results/truffelhog_results.json'
                }
            }
        }
        stage('Semgrep Scan') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh 'semgrep scan --config auto --output results/semgrep_results.json --json'
                }
            }
        }
    }
    post {
        always {
            echo 'Archiving artifacts...'
            archiveArtifacts artifacts: 'results/**/*', fingerprint: true, allowEmptyArchive: true
            echo 'Sending reports to DefectDojo...'
            echo 'Sending ZAP Scan...'
            // defectDojoPublisher(artifact: 'results/zap_xml_report.xml', 
            //     productName: 'Juice Shop', 
            //     scanType: 'ZAP Scan', 
            //     engagementName: 'novik21e@gmail.com')
            // echo 'Sending OSV Scan...'
            // defectDojoPublisher(artifact: 'results/sca-osv-scanner.json',
            //     productName: 'Juice Shop',
            //     scanType: 'OSV Scan',
            //     engagementName: 'novik21e@gmail.com')
            // echo 'Sending Trufflehog Scan...'
            // defectDojoPublisher(artifact: 'results/truffelhog_results.json', 
            //     productName: 'Juice Shop', 
            //     scanType: 'Trufflehog Scan', 
            //     engagementName: 'novik21e@gmail.com')
            echo 'Sending Semgrep Scan...'
            defectDojoPublisher(artifact: 'results/semgrep_results.json', 
                productName: 'Juice Shop', 
                scanType: 'Semgrep JSON Report', 
                engagementName: 'novik21e@gmail.com')
        }
    }
}
