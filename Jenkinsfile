pipeline {
    agent any

    environment {
        VENV_DIR = '.venv'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Ashish7i0/flask_ci_cd_app.git'
            }
        }

        stage('Set up virtual environment and install dependencies') {
            steps {
                sh '''
                    python3 -m venv $VENV_DIR
                    . $VENV_DIR/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    . $VENV_DIR/bin/activate
                    pytest
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@13.127.106.206 << 'EOF'
                    if [ -d "flask_ci_cd_app" ]; then
                        cd flask_ci_cd_app
                        git fetch origin main
                        git reset --hard origin/main
                    else
                        git clone https://github.com/Ashish7i0/flask_ci_cd_app.git
                        cd flask_ci_cd_app
                    fi

                    pkill gunicorn || true

                    if [ ! -d "venv" ]; then
                        python3 -m venv venv
                    fi

                    source venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    nohup gunicorn -w 3 -b 127.0.0.1:5000 app:app > gunicorn.log 2>&1 &
                    EOF
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and deployment succeeded!'
        }
        failure {
            echo '❌ Build or deployment failed.'
        }
    }
}