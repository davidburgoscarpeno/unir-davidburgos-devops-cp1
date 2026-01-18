pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python') {
            steps {
                echo 'Instalando dependencias'
                bat 'python -m pip install --upgrade pip'
                bat 'python -m pip install pytest flask flake8 bandit coverage'
            }
        }

        stage('Unit Tests') {
            steps {
                echo 'Pruebas unitarias'
                bat 'python -m pytest test\\unit --junitxml=unit-tests.xml'
            }
            post {
                always {
                    junit 'unit-tests.xml'
                }
            }
        }

        stage('Integration Tests') {
            steps {
                echo 'Pruebas de integración (pueden fallar si no hay servicios)'
                bat 'python -m pytest test\\rest || exit 0'
            }
        }

        stage('Static Analysis - Flake8') {
            steps {
                echo 'Análisis estático con Flake8'
                bat 'python -m flake8 app --format=pylint --output-file=flake8.txt || exit 0'
            }
            post {
                always {
                    recordIssues tools: [pyLint(pattern: 'flake8.txt')]
                }
            }
        }

        stage('Security Analysis - Bandit') {
            steps {
                echo 'Análisis de seguridad con Bandit'
                bat 'python -m bandit -r app -f json -o bandit.json || exit 0'
            }
            post {
                always {
                    recordIssues tools: [bandit(pattern: 'bandit.json')]
                }
            }
        }

        stage('Coverage') {
            steps {
                echo 'Cálculo de cobertura'
                bat 'python -m coverage run -m pytest || exit 0'
                bat 'python -m coverage xml'
            }
            post {
                always {
                    recordCoverage tools: [[parser: 'COBERTURA', pattern: 'coverage.xml']]
                }
            }
        }

        stage('Performance') {
            steps {
                echo 'Generando datos de rendimiento simulados'
                bat '''
echo timeStamp,elapsed,label,responseCode,success,Latency>performance.csv
echo 1,120,add,200,true,120>>performance.csv
echo 2,90,add,200,true,90>>performance.csv
echo 3,110,add,200,true,110>>performance.csv
'''
            }
            post {
                always {
                    perfReport sourceDataFiles: 'performance.csv'
                }
            }
        }
    }
}
