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

        stage('Setup Virtual Environment & Install Dependencies') {
            steps {
                sh '''
                    echo "🧪 Python version:" && \
                    python3 --version && \
                    echo "📦 Creating virtual environment..." && \
                    python3 -m venv $VENV_DIR && \
                    echo "🐍 Activating virtualenv and installing dependencies..." && \
                    . $VENV_DIR/bin/activate && \
                    echo "🔍 Python: $(which python)" && \
                    echo "🔍 Pip: $(which pip)" && \
                    pip install --upgrade pip && \
                    pip install -r requirements.txt && \
                    echo "✅ Dependencies installed:" && \
                    pip list
                '''
            }
        }

        stage('Run Tests with Pytest') {
            steps {
                sh '''
                    echo "🧪 Running tests..." && \
                    . $VENV_DIR/bin/activate && \
                    pytest
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@13.127.106.206 << 'EOF'
                    echo "🚀 Starting deployment..."

                    if [ -d "flask_ci_cd_app" ]; then
                        cd flask_ci_cd_app
                        git fetch origin main
                        git reset --hard origin/main
                    else
                        git clone https://github.com/Ashish7i0/flask_ci_cd_app.git
                        cd flask_ci_cd_app
                    fi

                    echo "🧹 Stopping any running Gunicorn processes..."
                    pkill gunicorn || true

                    echo "📦 Setting up virtual environment on EC2..."
                    if [ ! -d "venv" ]; then
                        python3 -m venv venv
                    fi

                    source venv/bin/activate && \
                    pip install --upgrade pip && \
                    pip install -r requirements.txt && \

                    echo "🚀 Starting Flask app using Gunicorn..."
                    nohup gunicorn -w 3 -b 127.0.0.1:5000 app:app > gunicorn.log 2>&1 &
                    echo "✅ Deployment complete!"
                    EOF
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD pipeline completed successfully!'
        }
        failure {
            echo '❌ CI/CD pipeline failed. Check console output.'
        }
    }
}