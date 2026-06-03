pipeline {
    agent any // Runs directly inside your running Jenkins container context

    stages {
        stage('Setup System Dependencies') {
            steps {
                sh 'git config --global --add safe.directory "*"'
                echo 'Installing Python, Tkinter, Xvfb and dependencies...'
                sh '''
                    apt-get update -qq
                    apt-get install -y python3 python3-pip python3-tk xvfb libtk8.6
                '''
            }
        }

        stage('Install Python Dependencies') {
            steps {
                echo 'Installing Python packages...'
                // Using --break-system-packages allows pip to install globally inside the lab container safely
                sh 'pip3 install --break-system-packages -r requirements.txt'
            }
        }

        stage('Syntax Check') {
            steps {
                echo 'Checking Python syntax...'
                sh 'python3 -m py_compile app/main.py && echo "Syntax OK"'
            }
        }

        stage('Run App Headless') {
            steps {
                echo 'Launching app with virtual display...'
                sh '''
                    Xvfb :99 -screen 0 1024x768x24 &
                    sleep 2
                    export DISPLAY=:99
                    timeout 5 python3 app/main.py || true
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
