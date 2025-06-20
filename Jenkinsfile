pipeline {
    agent any

    environment {
        // Ajuste esse caminho para o local exato do seu snyk.cmd
        SNYK_PATH = 'C:\\Users\\pablo\\AppData\\Roaming\\npm\\snyk.cmd'  
        SNYK_TOKEN = credentials('snyk-int3')
    }

    triggers {
        githubPush()
        // Pode incluir webhook PR se desejar
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/pablodlz/juice-shop.git'
            }
        }

        stage('Install dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('SAST com Snyk Code') {
            steps {
                bat "\"${env.SNYK_PATH}\" code test --json > snyk-sast-report.json"
            }
        }

        stage('SCA - Auditoria de Dependências') {
            steps {
                bat "\"${env.SNYK_PATH}\" test --json > snyk-sca-report.json"
            }
        }

        stage('IaC - Dockerfile scan') {
            steps {
                bat "\"${env.SNYK_PATH}\" iac test --json > snyk-iac-report.json"
            }
        }

        stage('Monitoramento no Snyk') {
            steps {
                bat "\"${env.SNYK_PATH}\" monitor"
            }
        }

        stage('Arquivar Relatórios') {
            steps {
                archiveArtifacts artifacts: '*.json', allowEmptyArchive: true
            }
        }
    }
}