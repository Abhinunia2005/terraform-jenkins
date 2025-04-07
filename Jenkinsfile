pipeline {
    agent any

    stages {
        stage('Terraform Init & Apply') {
            steps {
                echo 'Running Terraform Init, Plan, and Apply'
            }
        }

        stage('Publish and Deploy .NET 8 Web API') {
            steps {
                echo 'Publishing .NET 8 Web API and deploying to Azure'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
    }
}
