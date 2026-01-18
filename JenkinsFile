pipeline {
    agent any

    stages {

        stage('Unit Tests') {
            steps {
                echo 'Ejecutando pruebas unitarias'
                sh 'pytest test/unit || true'
            }
        }

        stage('Integration Tests') {
            steps {
                echo 'Ejecutando pruebas de integracion'
                sh 'pytest test/rest || true'
            }
        }

        stage('Static Analysis') {
            steps {
                echo 'Analisis estatico con flake8'
                sh 'flake8 app || true'
            }
        }

        stage('Security Tests') {
            steps {
                echo 'Analisis de seguridad con bandit'
                sh 'bandit -r app || true'
            }
        }

        stage('Coverage') {
            steps {
                echo 'Calculo de cobertura'
                sh '''
                    coverage run -m pytest
                    coverage report
                '''
            }
        }
    }
}

