pipeline {
    agent any

    environment {
        AZURE_CLIENT_ID     = credentials('azure-client-id')
        AZURE_CLIENT_SECRET = credentials('azure-client-secret')
        AZURE_SUBSCRIPTION_ID = credentials('azure-subscription-id')
        AZURE_TENANT_ID     = credentials('azure-tenant-id')
    }

    stages {
        stage('Terraform Init & Apply') {
            steps {
                dir('terraform') {
                    withEnv([
                        "ARM_CLIENT_ID=${AZURE_CLIENT_ID}",
                        "ARM_CLIENT_SECRET=${AZURE_CLIENT_SECRET}",
                        "ARM_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}",
                        "ARM_TENANT_ID=${AZURE_TENANT_ID}"
                    ]) {
                        bat 'terraform init'
                        bat 'terraform plan -out=tfplan'
                        bat 'terraform apply -auto-approve tfplan'
                    }
                }
            }
        }
    }
}
