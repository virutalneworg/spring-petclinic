pipeline {
  agent any
  environment {
    AZURE_SUBSCRIPTION_ID='${subscriptionid}'
    AZURE_TENANT_ID='${tenetid}'
    AZURE_STORAGE_ACCOUNT='storageforjenkinsbuild'
    CONTAINER_NAME = 'myjob'
    BLOB_NAME = '${JOB_NAME}'
    PACKAGE_PATH = '/home/ubuntu/ps2/spring-petclinic/target/spring-petclinic-3.0.0-SNAPSHOT.jar'
  }
  stages {
    stage('Build') {
      steps {
        sh 'mvn clean install'
      }

      post {
        success {
          withCredentials([usernamePassword(credentialsId: 'azuresp', 
                          passwordVariable: 'AZURE_CLIENT_SECRET', 
                          usernameVariable: 'AZURE_CLIENT_ID')]) {
            sh '''
              az storage blob upload --account-name $AZURE_STORAGE_ACCOUNT --auth-mode login --container-name myjob --name ${JOB_NAME} 
            '''
          }
        }
      }
    }
  }
}
