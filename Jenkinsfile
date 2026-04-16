pipeline {
    agent { label 'Slave1' }   // Run on your slave node

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Naseroodin/appdeploy.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                echo 'This is a dummy build stage — no compilation needed for static Apache app'
            }
        }

        stage('Install Apache') {
            steps {
                sh '''
                  # Update package lists and install Apache if missing
                  if [ -x "$(command -v apt-get)" ]; then
                    sudo apt-get update -y
                    sudo apt-get install -y apache2
                    sudo systemctl enable apache2
                    sudo systemctl start apache2
                  elif [ -x "$(command -v yum)" ]; then
                    sudo yum install -y httpd
                    sudo systemctl enable httpd
                    sudo systemctl start httpd
                  fi
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                  # Ensure web root exists
                  sudo mkdir -p /var/www/html

                  # Copy app files
                  sudo cp -r * /var/www/html/

                  # Restart Apache service
                  sudo systemctl restart apache2 || sudo systemctl restart httpd
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment successful! Apache is serving your app.'
        }
        failure {
            echo 'Deployment failed. Check logs and permissions.'
        }
    }
}
