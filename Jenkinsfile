pipeline{
    agent any
    stages{
        stage ('Git clone') {
            steps {
                git url: 'https://github.com/virtualram-rgb/spring-petclinic.git', branch: 'main'
            }
        }
        stage ('build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('exec maven'){
            environment {
                AZURE_SUBSCRIPTION_ID='${subscriptionid}'
                AZURE_TENANT_ID='${tenetid}'
                AZURE_STORAGE_ACCOUNT='storageforjenkinsbuild'
                CONTAINER_NAME = 'myjob'
                BLOB_NAME = '${JOB_NAME}'
            }
            steps {
        withCredentials([usernamePassword(credentialsId: 'azuresp', 
                        passwordVariable: 'AZURE_CLIENT_SECRET', 
                        usernameVariable: 'AZURE_CLIENT_ID')]) {
          sh '''
            # Login to Azure with ServicePrincipal
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            # Execute upload to Azure
            az storage blob upload --account-name $AZURE_STORAGE_ACCOUNT --container-name myjob --name ${JOB_NAME} --file ${WORKSPACE}/*.war --auth-mode login
            # Logout from Azure
            az logout
          '''   
        }
        }
    }
}
