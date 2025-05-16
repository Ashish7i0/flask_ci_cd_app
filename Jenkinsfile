pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Ashish7i0/flask_ci_cd_app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'pytest'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@13.127.106.206 << EOF
                    pkill gunicorn || true
                    cd flask_ci_cd_app || git clone https://github.com/Ashish7i0/flask_ci_cd_app.git && cd flask_ci_cd_app
                    git pull origin main
                    source venv/bin/activate || python3 -m venv venv && source venv/bin/activate
                    pip install -r requirements.txt
                    nohup gunicorn -w 3 -b 127.0.0.1:5000 app:app &
                    EOF
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and Deployment Successful!'
        }
        failure {
            echo '❌ Build or Deployment Failed.'
        }
    }
}