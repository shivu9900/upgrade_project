pipeline {
    agent any

    environment {
        AWS_KEY = "/var/lib/jenkins/.ssh/dr-keypair.pem"
        AZURE_KEY = "/var/lib/jenkins/.ssh/azurekey.pem"
        AWS_USER = "ubuntu"
        AZURE_USER = "azureuser"
        AWS_HOST = "44.204.190.114"
        AZURE_HOST = "20.121.42.105"
    }

    stages {

        stage('Pull Latest Files') {
            steps {
                echo "Pulled latest HTML from GitHub"
            }
        }

        stage('Deploy to AWS') {
            steps {
                sh """
                    ssh -o StrictHostKeyChecking=no -i \$AWS_KEY \$AWS_USER@\$AWS_HOST \
                    "sudo mv /var/www/html/index.html /var/www/html/index.html.bak || true"

                    scp -o StrictHostKeyChecking=no -i \$AWS_KEY index-aws.html \$AWS_USER@\$AWS_HOST:/tmp/index.html

                    ssh -i \$AWS_KEY \$AWS_USER@\$AWS_HOST \
                    "sudo mv /tmp/index.html /var/www/html/index.html && sudo systemctl restart nginx"
                """
            }
        }

        stage('Deploy to Azure') {
            steps {
                sh """
                    ssh -o StrictHostKeyChecking=no -i \$AZURE_KEY \$AZURE_USER@\$AZURE_HOST \
                    "sudo mv /var/www/html/index.html /var/www/html/index.html.bak || true"

                    scp -o StrictHostKeyChecking=no -i \$AZURE_KEY index-azure.html \$AZURE_USER@\$AZURE_HOST:/tmp/index.html

                    ssh -i \$AZURE_KEY \$AZURE_USER@\$AZURE_HOST \
                    "sudo mv /tmp/index.html /var/www/html/index.html && sudo systemctl restart nginx"
                """
            }
        }
    }

    post {
        success {
            echo "Deployment Successful!"
        }
        failure {
            echo "Deployment Failed!"
        }
    }
}

