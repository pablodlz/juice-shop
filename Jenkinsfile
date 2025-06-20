pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('snyk-int3')
        PATH = "C:\\Users\\pablo\\AppData\\Roaming\\npm;${env.PATH}"
    }

    stages {
        stage('SAST com Snyk Code (Rápido)') {
            steps {
                dir('.') {
                    bat 'snyk code test --all-projects --severity-threshold=high --json > snyk-sast-report.json'
                }
            }
        }

        stage('SCA - Auditoria de Dependências (Rápido)') {
            steps {
                dir('.') {
                    bat 'snyk test --all-projects --severity-threshold=high --json > snyk-sca-report.json'
                }
            }
        }

        stage('IaC - Dockerfile scan') {
            steps {
                dir('.') {
                    bat 'snyk iac test Dockerfile --json > snyk-iac-report.json'
                }
            }
        }

        stage('Monitoramento no Snyk') {
            steps {
                dir('.') {
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
