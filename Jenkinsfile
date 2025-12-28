pipeline {
  agent any
  environment {
    DOCKER_SERVER_IP = '13.51.47.39'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/SheharBano404/Jenkins.git'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('MySonar') {
          sh 'sonar-scanner'
        }
      }
    }

    stage('Build & Deploy on Docker Server') {
      steps {
        sshagent (credentials: ['docker-ssh']) {
          sh """
            ssh -o StrictHostKeyChecking=no ubuntu@$13.51.47.39'
              # Clean old repo
              rm -rf Jenkins
              # Clone fresh code
              git clone https://github.com/SheharBano404/Jenkins.git
              cd Jenkins
              # Build Docker image locally on Docker server
              docker build -t html-site:latest .
              # Stop old container if running
              docker rm -f html-site || true
              # Run new container
              docker run -d --name html-site -p 80:80 html-site:latest
            '
          """
        }
      }
    }
  }
}
