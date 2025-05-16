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
    }
}
