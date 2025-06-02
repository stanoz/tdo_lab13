pipeline {
   agent {
          docker { image 'stanoz03/custom-jenkins-agent:1.0.1';
          args '-u root -v /var/run/docker.sock:/var/run/docker.sock' }
      }

  environment {
    SONARQUBE_SERVER = 'http://sonarqube:9000'
    SONARQUBE_TOKEN = credentials('sonarqube-token')
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
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
     stage('Build') {
          steps {
            sh 'npm run build'
          }
        }
    stage('Test') {
      steps {
        sh 'npm test'
      }
      post {
          always {
              publishHTML(target: [
                  reportDir: 'coverage/lcov-report',
                  reportFiles: 'index.html',
                  reportName: 'Coverage Report'
               ])
          }
      }
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