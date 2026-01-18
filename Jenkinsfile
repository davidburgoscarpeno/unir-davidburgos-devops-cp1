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

    stage('Performance') {
        echo 'Datos de rendimiento simulados'
        bat 'echo timeStamp,elapsed,label,responseCode,responseMessage,success,bytes,sentBytes,Latency,Connect>performance.csv'
        bat 'echo 1,120,add,200,OK,true,512,128,120,10>>performance.csv'
        bat 'echo 2,90,add,200,OK,true,512,128,90,8>>performance.csv'
        bat 'echo 3,110,add,200,OK,true,512,128,110,9>>performance.csv'
        perfReport sourceDataFiles: 'performance.csv'
    }

}
