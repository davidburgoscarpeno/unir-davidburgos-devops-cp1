node {

    stage('Checkout') {
        checkout scm
    }

    stage('Setup Python') {
        echo 'Instalando dependencias'
        bat 'python -m pip install --upgrade pip'
        bat 'python -m pip install pytest flask flake8 bandit coverage'
    }

    stage('Unit Tests') {
        echo 'Pruebas unitarias'
        bat 'python -m pytest test\\unit --junitxml=unit-tests.xml'
        junit 'unit-tests.xml'
    }

    stage('Integration Tests') {
        echo 'Pruebas de integración (pueden fallar)'
        bat 'python -m pytest test\\rest || exit 0'
    }

    stage('Static Analysis - Flake8') {
        echo 'Análisis estático'
        bat 'python -m flake8 app --format=pylint --output-file=flake8.txt || exit 0'
        recordIssues tools: [pyLint(pattern: 'flake8.txt')]
    }

    stage('Security Analysis - Bandit') {
        echo 'Análisis de seguridad'
        bat 'python -m bandit -r app -f json -o bandit.json || exit 0'
        recordIssues tools: [issues(pattern: 'bandit.json')]
    }

    stage('Coverage') {
        echo 'Cobertura'
        bat 'python -m coverage run -m pytest || exit 0'
        bat 'python -m coverage xml'
        recordCoverage tools: [[parser: 'COBERTURA', pattern: 'coverage.xml']]
    }

 stage('Performance - JMeter') {
    steps {
        echo 'Ejecutando pruebas de rendimiento con JMeter'

        bat '''
        start /B python app\\api.py
        timeout /T 5
        '''

        bat '''
        "%JMETER_HOME%\\bin\\jmeter.bat" ^
          -n ^
          -t test\\jmeter\\flask.jmx ^
          -l performance.csv ^
          -e -o jmeter-report
        '''
    }
    post {
        always {
            perfReport sourceDataFiles: 'performance.csv'
        }
    }
}


}
