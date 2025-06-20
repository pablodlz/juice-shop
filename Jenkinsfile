pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('snyk-int3')
        PATH = "C:\\Users\\pablo\\AppData\\Roaming\\npm;${env.PATH}"
    }

    stages {
        stage('SAST com Snyk Code (Rápido)') {
            steps {
                dir('"C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop"') {
                    bat 'echo Iniciando Snyk SAST...'
                    bat 'cmd /c snyk --version'
                    bat 'cmd /c snyk code test --all-projects --severity-threshold=high --json > snyk-sast-report.json'
                    bat 'echo Snyk SAST finalizado.'
                }
            }
        }

        stage('SCA - Auditoria de Dependências (Rápido)') {
            steps {
                dir('"C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop"') {
                    bat 'echo Iniciando Snyk SCA...'
                    bat 'cmd /c snyk test --all-projects --severity-threshold=high --json > snyk-sca-report.json'
                    bat 'echo Snyk SCA finalizado.'
                }
            }
        }

        stage('IaC - Dockerfile scan') {
            steps {
                dir('"C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop"') {
                    bat 'echo Iniciando Snyk IaC...'
                    bat 'cmd /c snyk iac test Dockerfile --json > snyk-iac-report.json'
                    bat 'echo Snyk IaC finalizado.'
                }
            }
        }

        stage('Monitoramento no Snyk') {
            steps {
                dir('"C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop"') {
                    bat 'echo Iniciando monitoramento Snyk...'
                    bat 'cmd /c snyk monitor --all-projects'
                    bat 'echo Monitoramento Snyk finalizado.'
                }
            }
        }

        stage('Arquivar Relatórios') {
            steps {
                archiveArtifacts artifacts: '**/*.json', allowEmptyArchive: true
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
