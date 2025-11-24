pipeline {
  agent any
  environment {
    REGISTRY = "localhost:5001"
    IMAGE = "${env.REGISTRY}/devops-demo:${env.BUILD_NUMBER}"
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Install & Test') {
      steps {
        sh 'python -m venv .venv'
        sh '. .venv/bin/activate || source .venv/bin/activate; pip install -r app/requirements.txt; pip install pytest'
        sh '. .venv/bin/activate || source .venv/bin/activate; pytest -q'
      }
    }
    stage('Build Docker') {
      steps {
        sh "docker build -t ${IMAGE} ."
      }
    }
    stage('Push to Registry') {
      steps {
        sh "docker tag ${IMAGE} ${REGISTRY}/devops-demo:latest || true"
        sh "docker push ${IMAGE}"
      }
    }
    stage('Deploy') {
      steps {
        sh "docker-compose pull || true"
        sh "docker-compose up -d --build app"
      }
    }
  }
  post {
    always {
      sh 'docker image ls | head -n 20 || true'
    }
  }
}

