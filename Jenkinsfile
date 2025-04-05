pipeline {
    agent any

    stages {
        stage('Terraform Init & Apply') {
            steps {
                withCredentials([azureServicePrincipal(
                    credentialsId: 'AZURE_CREDENTIALS',
                    subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                    clientIdVariable: 'ARM_CLIENT_ID',
                    clientSecretVariable: 'ARM_CLIENT_SECRET',
                    tenantIdVariable: 'ARM_TENANT_ID'
                )]) {
                    dir('terraform') {
                        withEnv([
                            "ARM_CLIENT_ID=${ARM_CLIENT_ID}",
                            "ARM_CLIENT_SECRET=${ARM_CLIENT_SECRET}",
                            "ARM_SUBSCRIPTION_ID=${ARM_SUBSCRIPTION_ID}",
                            "ARM_TENANT_ID=${ARM_TENANT_ID}"
                        ]) {
                            bat 'terraform init'
                            bat 'terraform plan -out=tfplan'
                            bat 'terraform apply -auto-approve tfplan'
                        }
                    }
                }
            }
        }

        stage('Publish .NET 8 Web API') {
    steps {
        dir('webapi') {
            bat 'dotnet publish -c Release -o out'
            bat 'powershell Compress-Archive -Path out\\* -DestinationPath ..\\webapi.zip'
        }
    }
}


        stage('Deploy to Azure App Service') {
            steps {
                withCredentials([azureServicePrincipal(
                    credentialsId: 'AZURE_CREDENTIALS',
                    subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                    clientIdVariable: 'ARM_CLIENT_ID',
                    clientSecretVariable: 'ARM_CLIENT_SECRET',
                    tenantIdVariable: 'ARM_TENANT_ID'
                )]) {
                    bat 'az login --service-principal -u %ARM_CLIENT_ID% -p %ARM_CLIENT_SECRET% --tenant %ARM_TENANT_ID%'
                    bat 'az account set --subscription %ARM_SUBSCRIPTION_ID%'
                    bat 'az webapp deploy --resource-group terraform-jenkins --name terraform8414jenkins --src-path webapi.zip --type zip'
                }
            }
        }
    }
}
