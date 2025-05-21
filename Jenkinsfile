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
                    git credentialsId: 'github-pat', url: 'https://github.com/szym-i/abcd-student', branch: 'main'
                }
            }
        }
        // stage('[ZAP] Baseline passive-scan') {
        //     steps {
        //         sh 'mkdir -p ${WORKSPACE}/results'
		// sh 'mkdir -p "${WORKSPACE}/.zap/reports"'
        //         sh '''
        //             docker run --name juice-shop -d --rm \
        //                 -p 3000:3000 \
        //                 bkimminich/juice-shop
        //             sleep 5
        //         '''
        //         sh '''
        //             docker run --name zap \
        //                 --add-host=host.docker.internal:host-gateway \
        //                 -v "${WORKSPACE}/.zap/":/zap/wrk/:rw \
        //                 -t ghcr.io/zaproxy/zaproxy:stable bash -c \
        //                 "zap.sh -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/passive_scan.yaml" \
        //                 || true
        //         '''
        //     }
        //     post {
        //         always {
        //             sh '''
        //                 docker cp zap:/zap/wrk/reports/zap_html_report.html ${WORKSPACE}/results/zap_html_report.html
        //                 docker cp zap:/zap/wrk/reports/zap_xml_report.xml ${WORKSPACE}/results/zap_xml_report.xml
        //                 docker stop zap juice-shop
        //                 docker rm zap
        //             '''
        //             archiveArtifacts artifacts: 'results/zap_*.html, results/zap_*.xml', fingerprint: true
        //         }
        //     }
        // }
        stage('[OSV] Scan package-lock.json') {
            steps {
                sh '''
                    mkdir -p "${WORKSPACE}/tmp/osv-scan"
                    cp package-lock.json "${WORKSPACE}/tmp/osv-scan/"
                    chmod 777 "${WORKSPACE}/tmp/osv-scan/package-lock.json"
                    ls -l "${WORKSPACE}/tmp/osv-scan/package-lock.json"                 
                '''
                sh '''
                    docker run --rm \
                    -v "${WORKSPACE}/tmp/osv-scan/":/app/ \
                    ghcr.io/google/osv-scanner \
                    --lockfile /app/package-lock.json > osv-results.json
                '''
            }
            post {
                always {
                    archiveArtifacts artifacts: 'osv-results.json', fingerprint: true
                }
            }
        }
    }
}
