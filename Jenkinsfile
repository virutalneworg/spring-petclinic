pipeline {
  agent any
  environment {
    AZURE_SUBSCRIPTION_ID='${subscriptionid}'
    AZURE_TENANT_ID='${tenetid}'
    AZURE_STORAGE_ACCOUNT='storageforjenkinsbuild'
    BLOB_NAME = '${JOB_NAME}'
    PACKAGE_PATH = "${WORKSPACE}/target/*.jar"
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
              echo $container_name
              # Login to Azure with ServicePrincipal
              az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
              az storage blob upload --account-name $AZURE_STORAGE_ACCOUNT --container-name myjob --name '${BLOB_NAME}' --file '${PACKAGE_PATH}' --auth-mode login
            '''
          }
        }
      }
    }
  }
}
