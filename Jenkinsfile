pipeline {
    agent any

    stages {

        stage('Setup Python') {
            steps {
                echo 'Instalando dependencias Python'
                bat 'python -m pip install pytest flask flake8 bandit coverage'
            }
        }

        stage('Unit Tests') {
            steps {
                echo 'Ejecutando pruebas unitarias'
                bat 'python -m pytest test\\unit'
            }
        }

        stage('Integration Tests') {
            steps {
                echo 'Ejecutando pruebas de integracion'
                bat 'python -m pytest test\\rest || exit 0'
            }
        }

        stage('Static Analysis') {
            steps {
                echo 'Analisis estatico con flake8'
                bat 'python -m flake8 app || exit 0'
            }
        }

        stage('Security Tests') {
            steps {
                echo 'Analisis de seguridad con bandit'
                bat 'python -m bandit -r app || exit 0'
            }
        }

        stage('Coverage') {
            steps {
                echo 'Calculo de cobertura'
                bat 'python -m coverage run -m pytest || exit 0'
            }
        }
    }
}
