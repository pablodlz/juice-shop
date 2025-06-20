pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('snyk-int3') // ID da credencial no Jenkins
        PATH = "C:\\Users\\pablo\\AppData\\Roaming\\npm;${env.PATH}" // caminho onde o snyk está instalado
    }

    options {
        skipDefaultCheckout()
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                git credentialsId: 'github-login', url: 'https://github.com/pablodlz/juice-shop.git'
            }
        }

        stage('Install dependencies') {
            steps {
                bat 'npm config set registry https://registry.npmmirror.com'
                bat 'npm install || exit 0' // evita que o pipeline quebre por erro de rede temporário
            }
        }

        stage('SAST com Snyk Code') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    bat '"snyk code test --json > snyk-sast-report.json"'
                }
            }
        }

        stage('SCA - Auditoria de Dependências') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    bat 'snyk test --all-projects --json > snyk-sca-report.json'
                }
            }
        }

        stage('IaC - Dockerfile scan') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    bat 'snyk iac test Dockerfile --json > snyk-iac-report.json'
                }
            }
        }

        stage('Monitoramento no Snyk') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    bat 'snyk monitor --all-projects'
                }
            }
        }

        stage('Arquivar Relatórios') {
            steps {
                archiveArtifacts artifacts: '*.json', onlyIfSuccessful: false
            }
        }
    }

    post {
        always {
            echo 'Pipeline finalizado.'
        }
        failure {
            echo 'Erro detectado. Verifique os logs acima.'
        }
    }
}