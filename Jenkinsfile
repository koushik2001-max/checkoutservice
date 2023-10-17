
//def secrets = [
//    [path: 'secret/dockerhub']
//]
     def secrets_store = [
                          [
                              path: 'kv/dockerhubb',
                              secretValues: [
                                  [envVar: 'SECRET_KEY_1', secretKey: 'username'],
                                  [envVar: 'SECRET_KEY_2', secretKey: 'password']
                              ]
                          ]
                      ]
def configuration = [
    vaultUrl: 'http://3.108.254.106:8200',
    vaultCredentialId: 'testt-token',
    engineVersion: 2
]


pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    VAULT_ADDR = 'http://3.108.254.106/:8200/'
  }
  stages {


            stage('vaultt'){
           steps{
             //withVault(configuration: [timeout: 60, vaultCredentialId: 'vault-token', vaultUrl: 'http://13.233.251.37:8200'], vaultSecrets: [[engineVersion: 2,path: 'secret/dockerhub', secretValues: [[vaultKey: 'username'], [vaultKey: 'password']]]]) {
//withVault(configuration: [timeout: 60, vaultCredentialId: 'vault-approle' ,vaultUrl: 'http://13.233.251.37:8200'], vaultSecrets: [[path: 'secret/jenkins/dockerhub2', secretValues: [[envVar: 'mysecret', vaultKey: 'username']]]]) {
  // some block

       //   withVault([configuration: configuration, vaultSecrets: secrets]) {
       //   sh "echo ${env.username}"
       //   sh "echo ${env.password}"
      //}
 //sh 'echo $mysecret'


           //  withVault(configuration: [skipSslVerification: true, timeout: 60, vaultCredentialId: 'testt-token', vaultUrl: 'http://127.0.0.1:8200'], vaultSecrets: [[path: 'secret/dockerhub', secretValues: [[vaultKey: 'username'], [vaultKey: 'password']]]]) {
    // some block
            //  sh 'echo $username'
//}

             withCredentials([[$class: 'VaultTokenCredentialBinding', credentialsId: 'vault-token', vaultAddr: VAULT_ADDR]]) {
                // values will be masked
                sh 'echo TOKEN=$VAULT_TOKEN'
               sh 'echo $VAULT_TOKEN'
               sh 'echo $TOKEN'
               // sh 'echo ADDR=$VAULT_ADDR'
                
   //             withVault([configuration: [vaultUrl: VAULT_ADDR, vaultCredentialId: 'jenkins_token', engineVersion: 2], vaultSecrets: secrets]) {
          //          sh 'env'
             
//}
}
         }
     }



      stage('Access Vault Secrets') {
          steps {

                 withVault([configuration: configuration, vaultSecrets: secrets_store]) {
                echo "$SECRET_KEY_1"
                      echo "$SECRET_KEY_2"
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

stage('snyk checking') {

      steps {

        echo 'snyk testing...'

        snykSecurity(

          snykInstallation: "snyk@latest",

          snykTokenId: "organisation-snyk-api-token",

          // place other parameters here

        )

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
