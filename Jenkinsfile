pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
  }
  stages {


         stage('vaultt'){
           steps{
withVault(configuration: [timeout: 60, vaultCredentialId: 'vault-token', vaultUrl: 'http://13.235.70.144:8200'], vaultSecrets: [[path: 'secret/dockerhub', secretValues: [[vaultKey: 'username'], [vaultKey: 'password']]]]) {
    // some block
  sh 'echo $username'
  sh 'echo $password'
}
         }
         }



    
    
    
          stage('Docker Bench Security') {
      steps {
        sh 'chmod +x docker-bench-security.sh'
        sh './docker-bench-security.sh'
      }
    }



    
        stage('SonarQube Analysis') {
          agent any
      steps {
       

        sh '/var/opt/sonar-scanner-4.7.0.2747-linux/bin/sonar-scanner  -Dsonar.projectKey=checkout-service   -Dsonar.sources=.   -Dsonar.host.url=http://172.31.7.193:9000   -Dsonar.token=sqp_3ec0d083de10a3b34456bf69cab2f03c25d576c7'
      
        
      }
    }





    stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build('koushiksai/checkoutservice:latest', '.')
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
        sh 'docker push koushiksai/checkoutservice'
      }
    }
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}
