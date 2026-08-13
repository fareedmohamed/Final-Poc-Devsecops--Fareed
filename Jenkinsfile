pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/fareedmohamed/Final-Poc-Devsecops--Fareed.git'
            }
        }

        stage('Install & Build') {
            steps {
                sh '''
                    cd src
                    npm install
                    npm run build
                '''
            }
        }

        /* stage('Trivy Security Scan') {
            steps {
                sh 'trivy fs .'
            }
        } */

       stage('Deploy') {
           steps {
                sh '''
                   sudo rm -rf /var/www/html/*
                   sudo cp -r src/dist/* /var/www/html/
                  '''
              }
          }

        stage('OWASP ZAP Scan') {
            steps {
                sh '''
                    docker run --rm --network="host" \
                      -v $(pwd):/zap/wrk/:rw \
                      -t ghcr.io/zaproxy/zaproxy:stable \
                      zap-baseline.py \
                      -t http://localhost:80 \
                      -r report.html || true
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'report.html',
                             allowEmptyArchive: true,
                             fingerprint: true
        }

        success {
            echo 'Pipeline successful!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
