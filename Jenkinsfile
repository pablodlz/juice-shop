pipeline {
    agent any

    environment {
        SNYK_PATH = 'C:\\Users\\pablo\\AppData\\Roaming\\npm\\snyk.cmd'
        SNYK_TOKEN = credentials('snyk-int3')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/pablodlz/juice-shop.git'
            }
        }

        stage('SAST com Snyk Code') {
            steps {
                script {
                    def status = bat(script: "\"${env.SNYK_PATH}\" code test --json > snyk-sast-report.json", returnStatus: true)
                    if (status != 0) {
                        echo "Snyk Code test encontrou vulnerabilidades."
                    }
                }
            }
        }

        stage('SCA - Auditoria de Dependências') {
            steps {
                script {
                    def status = bat(script: "\"${env.SNYK_PATH}\" test --json > snyk-sca-report.json", returnStatus: true)
                    if (status != 0) {
                        echo "Snyk Test encontrou vulnerabilidades."
                    }
                }
            }
        }

        stage('IaC - Dockerfile scan') {
            steps {
                script {
                    def status = bat(script: "\"${env.SNYK_PATH}\" iac test --json > snyk-iac-report.json", returnStatus: true)
                    if (status != 0) {
                        echo "Snyk IaC encontrou vulnerabilidades."
                    }
                }
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