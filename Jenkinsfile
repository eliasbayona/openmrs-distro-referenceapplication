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
    stage('Build & Deploy') {
      steps {
        sh '''
          set -euxo pipefail
          [ -f .env ] && export $(grep -v '^#' .env | xargs) || true
          ${COMPOSE} pull || true
          ${COMPOSE} build --pull
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
    failure { sh 'docker compose logs --no-color || true' }
  }
}
