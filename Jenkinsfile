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
            ssh -o StrictHostKeyChecking=no ubuntu@$18.140.200.242 << 'EOF'
              # Clean old repo
              rm -rf Jenkins
              # Clone fresh code
              git clone https://github.com/SheharBano404/Jenkins.git
              cd Jenkins
              # Build Docker image locally on Docker server
              sudo docker build -t html-site:latest .
              # Stop old container if running
              sudo docker rm -f html-site || true
              # Run new container
              sudo docker run -d --restart always --name html-site -p 80:80 html-site:latest
            EOF
          """
        }
      }
    }
  }
}
