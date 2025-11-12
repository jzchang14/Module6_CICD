pipeline {
  agent any

  environment {
    INCOMING_URL = credentials('webex-incoming-url')
  }

  stages {
    stage('CI (build+test)') {
      steps {
        sh '''
          set -eux
          python3 -m venv .venv
          . .venv/bin/activate
          pip install -U pip
          pip install -r requirements.txt
          pytest -q --ignore-glob="*.txt"
          pycodestyle --max-line-length=100 app.py || true
        '''
      }
    }
  }

  post {
    success {
      httpRequest(
        httpMode: 'POST',
        url: "${INCOMING_URL}",
        contentType: 'APPLICATION_JSON',
        requestBody: '{"text": "✅ Build succeeded"}'
      )
    }
    failure {
      httpRequest(
        httpMode: 'POST',
        url: "${INCOMING_URL}",
        contentType: 'APPLICATION_JSON',
        requestBody: '{"text": "❌ Build failed"}'
      )
    }
  }
}
