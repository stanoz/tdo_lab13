pipeline {
   agent {
          docker { image 'stanoz03/custom-jenkins-agent:1.0.1';
          args '-u root --network tdo_lab13_ci-network -v /var/run/docker.sock:/var/run/docker.sock' }
      }

  environment {
    SONARQUBE_SERVER = 'SonarQube'
    SONARQUBE_URL = 'http://localhost:9000'
    SONAR_TOKEN = credentials('sonarqube-token-id')
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Setup Node.js') {
            steps {
                sh '''
                    apt-get update && apt-get install -y curl
                    curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
                    apt-get install -y nodejs
                    node -v
                    npm -v
                '''
            }
        }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Lint') {
      steps {
        sh 'npm run lint'
      }
    }
    stage('Test') {
      steps {
        sh 'npm test'
      }
      // post {
      //     always {
      //         publishHTML(target: [
      //             reportDir: 'coverage/lcov-report',
      //             reportFiles: 'index.html',
      //             reportName: 'Coverage Report'
      //          ])
      //     }
      // }
    }

    stage('SonarQube Analysis') {
      steps {
        script {
              def scannerHome = tool 'SonarQubeScanner'
              withSonarQubeEnv('SonarQube') {
                 sh (
                     script: """
                          ${scannerHome}/bin/sonar-scanner \
                          -Dsonar.projectKey=your_project_key \
                          -Dsonar.sources=. \
                          -Dsonar.host.url=${env.SONARQUBE_URL} \
                          -Dsonar.login=${env.SONAR_TOKEN}
                      """,
                      returnStdout: false
                  )
              }
            }
        }
    }
    }
}