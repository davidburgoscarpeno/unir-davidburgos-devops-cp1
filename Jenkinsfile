pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Setup Python') {
      steps {
        echo 'Instalando dependencias'
        bat 'python -m pip install --upgrade pip'
        bat 'python -m pip install pytest flask flake8 bandit coverage || exit 0'
      }
    }

    stage('Start Services (Windows)') {
      steps {
        echo 'Arrancando la app y mocks en background y esperando a que estén disponibles'
        bat '''
start /B python app.py
start /B python mock_service.py --port=9090
powershell -command "$i=0; while(-not (Test-NetConnection -ComputerName 'localhost' -Port 5000 -InformationLevel Quiet) -and $i -lt 30){Start-Sleep -Seconds 1; $i++}; if($i -ge 30){Write-Error 'Service on 5000 not available'; exit 1}"
'''
      }
    }

    stage('Unit Tests') {
      steps {
        echo 'Pruebas unitarias'
        bat 'python -m pytest test\\unit --junitxml=unit-tests.xml'
      }
      post {
        always { junit 'unit-tests.xml' }
      }
    }

    stage('Integration Tests') {
      steps {
        echo 'Pruebas de integración'
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
          recordIssues tools: [pylint(pattern: 'flake8.txt')], skipIfUnavailable: true
        }
      }
    }

    stage('Security Analysis - Bandit') {
      steps {
        echo 'Análisis de seguridad con Bandit (CLI)'
        bat 'python -m bandit -r app -f json -o bandit.json || exit 0'
      }
      post {
        always {
          recordIssues tools: [bandit(pattern: 'bandit.json')], skipIfUnavailable: true
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: '**/flake8.txt, **/bandit.json', allowEmptyArchive: true
    }
  }
}
