pipeline {
    agent any
    environment {
        VENV_DIR = ".venv"
    }
    stages {
        stage('Setup') {
            steps {
                sh 'python3 -m venv $VENV_DIR'
                sh 'source $VENV_DIR/bin/activate && pip install -r requirements.txt && playwright install'
            }
        }
        stage('Smoke Tests') {
            steps {
                sh 'source $VENV_DIR/bin/activate && pytest -m smoke --alluredir=allure-results-smoke --junitxml=allure-results-smoke/junit-smoke.xml'
            }
            post {
                always {
                    archiveArtifacts artifacts: 'allure-results-smoke/**', allowEmptyArchive: true
                }
            }
        }
        stage('E2E Tests') {
            steps {
                sh 'source $VENV_DIR/bin/activate && pytest -m e2e --alluredir=allure-results-e2e --junitxml=allure-results-e2e/junit-e2e.xml'
            }
            post {
                always {
                    archiveArtifacts artifacts: 'allure-results-e2e/**', allowEmptyArchive: true
                }
            }
        }
        stage('API Tests') {
            steps {
                sh 'source $VENV_DIR/bin/activate && pytest -m api --alluredir=allure-results-api --junitxml=allure-results-api/junit-api.xml'
            }
            post {
                always {
                    archiveArtifacts artifacts: 'allure-results-api/**', allowEmptyArchive: true
                }
            }
        }
    }
    post {
        always {
            junit 'allure-results-*/junit-*.xml'
            // Publish Allure reports if plugin is installed
            allure([
                includeProperties: false,
                jdk: '',
                results: [[path: 'allure-results-smoke']],
                reportBuildPolicy: 'ALWAYS',
                results: [[path: 'allure-results-e2e']],
                results: [[path: 'allure-results-api']]
            ])
        }
        cleanup {
            sh 'rm -rf $VENV_DIR allure-results-*'
        }
        success {
            slackSend(channel: '#test-result', color: 'good', message: "Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
        }
        failure {
            slackSend(channel: '#test-result', color: 'danger', message: "Build FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}")
        }
    }
}
