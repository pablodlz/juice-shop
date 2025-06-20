pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('snyk-int3') // ID da credencial no Jenkins
        PATH = "C:\\Users\\pablo\\AppData\\Roaming\\npm;${env.PATH}" // Onde o Snyk foi instalado via npm
    }

    options {
        timestamps()
    }

    stages {
        stage('Preparar código') {
            steps {
                dir('C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop') {
                }
            }
        }

        stage('Instalar dependências') {
            steps {
                dir('C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop') {
                    bat 'npm install'
                }
            }
        }

        stage('SAST com Snyk Code') {
            steps {
                dir('C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop') {
                    bat 'snyk code test --json > snyk-sast-report.json'
                }
            }
        }

        stage('SCA - Auditoria de Dependências') {
            steps {
                dir('C:/Users/pablo/OneDrive/Área de Trabalho/AppSec/Desafio/juice-shop') {
                    bat 'snyk test --all-projects --json > snyk-sca-report.json'
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
