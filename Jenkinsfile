pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
  }
  stages {
#    stage('Clone sources') {
#            steps {
#                git url: 'https://github.com/tkgregory/sonarqube-jacoco-code-coverage.git'
#            }
#        }
#    stage('SonarQube analysis') {
#            steps {
#                withSonarQubeEnv('SonarQube') {
#                    sh "./gradlew sonarqube"
#                }
#            }
#        }
#        stage("Quality gate") {
#            steps {
#                waitForQualityGate abortPipeline: true
#            }
#        }
#    stage('Build') {
#      steps {
#        sh 'docker build -t koushiksai/jenkins-docker-hub .'
#      }
#    }
    stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build('koushiksai/jenkins-docker-hub:latest', '-f .')
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('http://13.126.48.106:9000/') {
                        sh 'sonar-scanner -Dsonar.projectKey=sqp_16f943749daef60611cca3d8c077b69f685f95e1 -Dsonar.sources=. -Dsonar.language=docker'
                    }
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
