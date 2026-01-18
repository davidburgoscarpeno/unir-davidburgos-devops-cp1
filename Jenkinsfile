pipeline {
    agent any

    stages {

        stage('Unit Tests') {
            steps {
                echo 'Ejecutando pruebas unitarias'
                bat 'pytest test/unit || true'
            }
        }

        stage('Integration Tests') {
            steps {
                echo 'Ejecutando pruebas de integracion'
                bat 'pytest test/rest || true'
            }
        }

        stage('Static Analysis') {
            steps {
                echo 'Analisis estatico con flake8'
                bat 'flake8 app || true'
            }
        }

        stage('Security Tests') {
            steps {
                echo 'Analisis de seguridad con bandit'
                bat 'bandit -r app || true'
            }
        }

        stage('Coverage') {
            steps {
                echo 'Calculo de cobertura'
                bat '''
                    coverage run -m pytest
                    coverage report
                '''
            }
        }
    }
}

