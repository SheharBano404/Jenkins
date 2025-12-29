pipeline {
  agent any
  environment {
    DOCKER_SERVER_IP = '18.140.200.242'
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
            ssh -o StrictHostKeyChecking=no ubuntu@${DOCKER_SERVER_IP} << 'EOF'
              # Clean old repo
              rm -rf Jenkins
              # Clone fresh code
              git clone https://github.com/SheharBano404/Jenkins.git
              cd Jenkins
              # Force rebuild Docker image (no cache)
              sudo docker build --no-cache -t html-site:latest .
              # Stop old container if running
              sudo docker rm -f html-site || true
              # Run new container with restart policy
              sudo docker run -d --restart always --name html-site -p 80:80 html-site:latest
            EOF
          """
        }
      }
    }
  }
}
