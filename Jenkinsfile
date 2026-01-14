pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Divyjain0212/python-ci-cd-project.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . venv/bin/activate
                pytest
                '''
            }
        }

        stage('Deploy to AWS') {
            steps {
                sshagent(credentials: ['aws-ec2-key']) {
                sh '''
                ssh ec2-user@13.126.47.76 << EOF
                  sudo dnf install git -y

                  if [ ! -d "/home/ec2-user/python-ci-cd-project" ]; then
                    git clone https://github.com/Divyjain0212/python-ci-cd-project.git
                  fi

                  cd /home/ec2-user/python-ci-cd-project || exit 1
                  git pull
                  pip3 install -r requirements.txt
                  nohup python3 app.py > app.log 2>&1 &
                EOF
                '''
                }
            }
        }
    }

    post {
        success {
            echo 'Build, Test, and Deployment Successful'
        }
        failure {
            echo 'Pipeline Failed'
        }
    }
}
