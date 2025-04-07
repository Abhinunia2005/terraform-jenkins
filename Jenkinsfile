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

        stage('Publish and Fake Deploy .NET 8 Web API') {
            steps {
                dir('webapi') {
                    bat 'dotnet publish -c Release -o out'
                    bat 'if exist ..\\webapi.zip del ..\\webapi.zip'
                    bat 'powershell Compress-Archive -Path out\\* -DestinationPath ..\\webapi.zip'
                }

                // Simulate a deploy instead of doing the real one
                echo 'az webapp deploy --resource-group terraform-jenkins --name terraform8414jenkins --src-path webapi.zip --type zip'
                echo 'WARNING: Initiating deployment'
                echo 'WARNING: Deploying from local path: webapi.zip'
                echo 'WARNING: Warming up Kudu before deployment.'
                echo 'WARNING: Warmed up Kudu instance successfully.'
                echo 'Simulating deployment...'
                sleep time: 60, unit: 'SECONDS'
                echo 'Deployment succeeded (faked).'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
    }
}
