pipeline {
    agent any

    environment {
        REMOTE_USER = 'iammarta'
        REMOTE_HOST = '192.168.64.2'
    }

    stages {
        stage('Install Apache on Remote VM') {
            steps {
                sshagent(credentials: ['jenkins-ssh-jenkinskey']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST "
                        sudo apt update &&
                        sudo apt install -y apache2 &&
                        sudo systemctl start apache2 &&
                        sudo systemctl enable apache2
                    "
                    '''
                }
            }
        }

        stage('Check Apache status') {
            steps {
                sshagent(credentials: ['jenkins-ssh-jenkinskey']) {
                    sh '''
                    ssh $REMOTE_USER@$REMOTE_HOST "
                        sudo systemctl status apache2 | grep Active
                    "
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
