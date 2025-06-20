pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('snyk-int3')
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/pablodlz/juice-shop.git'
            }
        }

        stage('Install dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('SAST com Snyk Code') {
            steps {
                bat 'snyk code test --json > snyk-sast-report.json'
            }
        }

        stage('SCA - Auditoria de Dependências') {
            steps {
                bat 'snyk test --json > snyk-sca-report.json'
            }
        }

        stage('IaC - Dockerfile scan') {
            steps {
                bat 'snyk iac test --json > snyk-iac-report.json'
            }
        }

        stage('Monitoramento no Snyk') {
            steps {
                bat 'snyk monitor'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '*.json', allowEmptyArchive: true
        }
    }
}
