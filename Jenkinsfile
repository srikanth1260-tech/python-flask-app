pipeline {
    agent any

    environment {
        SERVER_IP = credentials('prod-server-ip')
        GIT_SCM = 'https://github.com/srikanth1260-tech/python-flask-app.git'
    }
    stages {
        stage("checkout") {
            steps {
                git url: "$GIT_SCM", branch: 'main'
            }
        }
        stage('Setup') {
            steps {
                sh "pip install -r requirements.txt"
            }
        }
        stage('Test') {
            steps {
                sh "pytest"
            }
        }
        stage('Package code') {
            steps {
                sh "zip -r python.zip ./* -x '*.git*'"
                sh "ls -lart"
            }
        }
        stage('Deploy to Prod') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-key', keyFileVariable: 'MY_SSH_KEY', usernameVariable: 'username')]) {
                    sh '''
                    scp -i $MY_SSH_KEY -o StrictHostKeyChecking=no python.zip  ${username}@${SERVER_IP}:/home/ec2-user/
                    ssh -i $MY_SSH_KEY -o StrictHostKeyChecking=no ${username}@${SERVER_IP} << EOF
                        unzip -o /home/ec2-user/python.zip -d /home/ec2-user/app/
                        source python/venv/bin/activate
                        cd /home/ec2-user/python/
                        pip install -r requirements.txt
                        sudo systemctl restart flaskapp.service
EOF
                    '''
                }
            }
        }
       
        
       
        
    }
}
