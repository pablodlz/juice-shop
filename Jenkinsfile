pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('snyk-int3')
        PATH = "C:\\Users\\pablo\\AppData\\Roaming\\npm;${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Instalar Dependências') {
            steps {
                bat 'npm install'
            }
        }

        stage('SAST - Snyk Code') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat 'snyk code test --all-projects --severity-threshold=high --json > snyk-sast-report.json'
                }
            }
        }

        stage('SCA - Dependências') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat 'snyk test --all-projects --severity-threshold=high --json > snyk-sca-report.json'
                }
            }
        }

        stage('IaC - Dockerfile') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat 'snyk iac test --severity-threshold=high --json > snyk-iac-report.json'
                }
            }
        }

        stage('Monitoramento') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    bat 'snyk monitor'
                }
            }
        }

        stage('Arquivar Relatórios') {
            steps {
                archiveArtifacts artifacts: '*.json', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Pipeline finalizado.'
        }
        failure {
            echo 'Erro detectado. Verifique os logs.'
        }
    }
}