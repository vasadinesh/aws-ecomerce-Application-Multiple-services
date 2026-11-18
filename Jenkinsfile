
pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/vasadinesh/aws-ecomerce-Application-Multiple-services.git'
        APP_DIR = '/opt/ecommerce'
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: "${REPO_URL}"
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    sudo apt-get update
                    sudo apt-get install -y git python3 python3-pip python3-venv nginx

                    python3 -m venv backend/venv
                    backend/venv/bin/pip install --upgrade pip
                    backend/venv/bin/pip install -r backend/requirements.txt
                '''
            }
        }

        stage('Backend Validation') {
            steps {
                sh '''
                    python3 -m py_compile backend/app.py
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                    sudo mkdir -p ${APP_DIR}

                    tar --exclude='venv' -cf - backend frontend | sudo tar -xf - -C ${APP_DIR}

                    sudo chown -R jenkins:jenkins ${APP_DIR}

                    sudo mkdir -p /var/www/html
                    sudo cp -r frontend/* /var/www/html/

                    sudo systemctl enable nginx
                    sudo systemctl restart nginx
                '''
            }
        }
    }

    post {
        success {
            echo 'E-commerce application deployed successfully!'
        }

        failure {
            echo 'Deployment failed. Check Jenkins console output.'
        }
    }
}
