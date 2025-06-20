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
                git 'https://github.com/pablodlz/juice-shop.git'
            }
        }
        
        stage('SAST com Snyk Code') {
            steps {
                sh 'snyk code test'
            }
        }

        stage('SCA - Auditoria de Dependências') {
            steps {
                sh 'snyk test'
            }
        }

        stage('IaC - Dockerfile scan') {
            steps {
                sh 'snyk iac test'
            }
        }

        stage('Monitoramento no Snyk') {
            steps {
                sh 'snyk monitor'
            }
        }

        stage('Gerar Relatórios Locais') {
            steps {
                sh 'snyk test --json > snyk-sca-report.json'
                sh 'snyk code test --json > snyk-sast-report.json'
                sh 'snyk iac test --json > snyk-iac-report.json'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '*.json', allowEmptyArchive: true
        }
    }
}
