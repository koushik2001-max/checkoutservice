pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
  }
  stages {

  stage('Docker Bench Security') {
      steps {
        sh 'chmod +x docker-bench-security.sh'
        sh './docker-bench-security.sh'
      }
    }
    
        stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv() {
        sh '/var/opt/sonar-scanner-4.7.0.2747-linux/bin/sonar-scanner  -Dsonar.projectKey=checkout-service   -Dsonar.sources=.   -Dsonar.host.url=http://172.31.7.193:9000   -Dsonar.token=sqp_3ec0d083de10a3b34456bf69cab2f03c25d576c7'

      }
      }
    }
stage('Quality Gate Check') {
            steps {
                script {
                    def serverUrl = 'http://your-sonarqube-server-url'
                    def projectKey = 'your-project-key'

                    def qualityGateStatus = sh(script: """
                        curl -s -u sonarqube-token: -X GET "http://172.31.7.193:9000/api/qualitygates/project_status?projectKey=sqp_3ec0d083de10a3b34456bf69cab2f03c25d576c7" | jq -r '.projectStatus.status'
                    """, returnStdout: true).trim()

                    if (qualityGateStatus != 'OK') {
                        currentBuild.result = 'FAILURE'
                        error "Quality Gate failed: ${qualityGateStatus}"
                    }
                }
            }
        }

    stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build('koushiksai/jenkins-docker-hub:latest', '.')
                }
            }
        }


    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('Push') {
      steps {
        sh 'docker push koushiksai/jenkins-docker-hub'
      }
    }
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}
