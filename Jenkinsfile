pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('snyk-int3')
        PATH = "C:\\Users\\pablo\\AppData\\Roaming\\npm;${env.PATH}"
    }

    stages {
        stage('SAST com Snyk Code (Rápido)') {
            steps {
                dir('C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop') {
                    bat 'snyk code test --path=./routes --severity-threshold=high --json > snyk-sast-report.json'
                    bat 'snyk code test --path=./routes --severity-threshold=high'
                }
            }
        }

        // Para ativar as etapas abaixo, tire o comentário (remova /* e */)

        /*
        stage('SCA - Auditoria de Dependências (Rápido)') {
            steps {
                dir('C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop') {
                    bat 'snyk test --all-projects --severity-threshold=high --json > snyk-sca-report.json'
                }
            }
        }

        stage('IaC - Dockerfile scan') {
            steps {
                dir('C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop') {
                    bat 'snyk iac test Dockerfile --json > snyk-iac-report.json'
                }
            }
        }
*/
        stage('Monitoramento no Snyk') {
            steps {
                dir('C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop') {
                    bat 'snyk monitor --all-projects'
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
