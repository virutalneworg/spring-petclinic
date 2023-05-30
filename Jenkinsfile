pipeline{
    agent any
    environment {
                AZURE_SUBSCRIPTION_ID='${subscriptionid}'
                AZURE_TENANT_ID='${tenetid}'
                AZURE_STORAGE_ACCOUNT='storageforjenkinsbuild'
                CONTAINER_NAME = 'myjob'
                BLOB_NAME = '${JOB_NAME}'
            }
    stages{
        stage ('Git clone') {
            steps {
                git url: 'https://github.com/virtualram-rgb/spring-petclinic.git', branch: 'main'
            }
        }
        stage ('build') {
            steps {
                sh 'mvn clean install'
            }
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
              # Set default subscription
              az account set --subscription $AZURE_SUBSCRIPTION_ID
              # Execute upload to Azure
              az storage blob upload-batch --destination ${JOB_NAME} --source ${WORKSPACE}/*.war --account-name $AZURE_STORAGE_ACCOUNT --auth-mode login
              # Logout from Azure
              az logout
            '''
          }
        }
      }
    }
}

