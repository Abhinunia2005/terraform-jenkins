pipeline {
    agent any
    environment {
        ARM_CLIENT_ID = credentials('AZURE_CREDENTIALS_CLIENT_ID')
        ARM_CLIENT_SECRET = credentials('AZURE_CREDENTIALS_CLIENT_SECRET')
        ARM_SUBSCRIPTION_ID = credentials('AZURE_CREDENTIALS_SUBSCRIPTION_ID')
        ARM_TENANT_ID = credentials('AZURE_CREDENTIALS_TENANT_ID')
    }
    stages {
        stage('Terraform Init') {
            steps {
                dir('terraform') {
                    bat 'terraform init'
                }
            }
        }

        // Siddhesh Prabhugaonkar
        // Cloud Authority

        stage('Terraform Plan & Apply') {
            steps {
                dir('terraform') {
                    bat 'terraform plan -out=tfplan'
                    bat 'terraform apply -auto-approve tfplan'
                }
            }
        }

        stage('Publish .NET 8 Web API') {
            steps {
                dir('webapi') {
                    bat 'dotnet publish -c Release -o out'
                }
            }
        }

        stage('Deploy to Azure App Service') {
            steps {
                bat 'az login --service-principal -u %ARM_CLIENT_ID% -p %ARM_CLIENT_SECRET% --tenant %ARM_TENANT_ID%'
                bat 'az account set --subscription %ARM_SUBSCRIPTION_ID%'
                bat 'az webapp deploy --resource-group terraform-jenkins --name terraform8414jenkins --src-path webapi\\out --type zip'
            }
        }
    }
}
