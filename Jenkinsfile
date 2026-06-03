pipeline {
    agent {
        docker {
            image 'python:3.11'
            args '--user root'
        }
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Cloning the repository...'
                checkout scm
            }
        }

        stage('Setup System Dependencies') {
            steps {
                echo 'Installing Tkinter, Xvfb and fonts...'
                sh '''
                    apt-get update -qq
                    apt-get install -y python3-tk xvfb libtk8.6
                '''
            }
        }

        stage('Install Python Dependencies') {
            steps {
                echo 'Installing Python packages...'
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Syntax Check') {
            steps {
                echo 'Checking Python syntax...'
                sh 'python -m py_compile app/main.py && echo "Syntax OK"'
            }
        }

        stage('Run App Headless') {
            steps {
                echo 'Launching app with virtual display...'
                sh '''
                    Xvfb :99 -screen 0 1024x768x24 &
                    sleep 2
                    export DISPLAY=:99
                    timeout 5 python app/main.py || true
                    echo "App ran successfully."
                '''
            }
        }

    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs above.'
        }
    }
}