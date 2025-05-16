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

        stage('Setup Virtual Environment and Install Requirements') {
            steps {
                sh '''
                    echo "🧪 Python version:"
                    python3 --version || exit 1

                    echo "📦 Creating virtual environment..."
                    python3 -m venv $VENV_DIR || exit 1

                    echo "🐍 Activating virtual environment and installing dependencies..."
                    . $VENV_DIR/bin/activate
                    echo "🔍 Using Python: $(which python)"
                    echo "🔍 Using Pip: $(which pip)"
                    pip install --upgrade pip || exit 1
                    pip install -r requirements.txt || exit 1

                    echo "📦 Installed packages:"
                    pip list
                '''
            }
        }

        stage('Run Pytest in venv') {
            steps {
                sh '''
                    echo "🧪 Running tests..."
                    . $VENV_DIR/bin/activate
                    which pytest || { echo "❌ Pytest not found"; exit 1; }
                    pytest || exit 1
                '''
            }
        }
    }

    post {
        always {
            echo '🧹 Cleanup stage (if needed)'
        }
        success {
            echo '✅ Build succeeded!'
        }
        failure {
            echo '❌ Build failed. Check console output.'
        }
    }
}