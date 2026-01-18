node {

stage('Checkout') {
    checkout scm
}

stage('Setup Python') {
    echo 'Instalando dependencias'
    bat 'python -m pip install --upgrade pip'
    bat 'python -m pip install pytest flask flake8 bandit coverage'
}

stage('Start Flask API') {
    echo 'Arrancando API Flask en background (puerto 9090)'
    bat '''
    start /B python -c "from app.api import api_application; api_application.run(host='127.0.0.1', port=9090)"
    ping 127.0.0.1 -n 6 > nul
    '''
}


stage('Unit Tests') {
    echo 'Pruebas unitarias'
    bat 'python -m pytest test\\unit --junitxml=unit-tests.xml'
    junit 'unit-tests.xml'
}

stage('Integration Tests') {
    echo 'Pruebas de integración REST'
    bat 'python -m pytest test\\rest'
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
    bat 'python -m coverage run -m pytest'
    bat 'python -m coverage xml'
    recordCoverage tools: [[parser: 'COBERTURA', pattern: 'coverage.xml']]
}

stage('Performance - JMeter') {
    echo 'Pruebas de rendimiento con JMeter'
    bat '''
    "%JMETER_HOME%\\bin\\jmeter.bat" ^
      -n ^
      -t test\\jmeter\\flask.jmx ^
      -l performance.csv ^
      -e -o jmeter-report
    '''
    perfReport sourceDataFiles: 'performance.csv'
}

}
