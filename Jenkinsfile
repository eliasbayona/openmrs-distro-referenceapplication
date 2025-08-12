pipeline {
  agent any
  options { timestamps() }
  environment { COMPOSE = "docker compose" } // or "docker-compose" if v1
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main',
            url: 'https://github.com/eliasbayona/openmrs-distro-referenceapplication.git',
            credentialsId: 'github-pat'
      }
    }
    stage('Deploy') {
      steps {
        sh '''
          set -euxo pipefail
          # Safely load .env if present (handles spaces and quotes)
          if [ -f .env ]; then
            set -a
            . ./.env
            set +a
          fi
          ${COMPOSE} up -d
          ${COMPOSE} ps
        '''
      }
    }
    stage('Health Check') {
      when { expression { return fileExists('healthcheck.sh') } }
      steps { sh 'bash healthcheck.sh' }
    }
  }
  post {
    failure {
      sh '''
        set -e
        ${COMPOSE} logs --no-color || true
      '''
    }
  }
}
