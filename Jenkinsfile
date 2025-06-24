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

        stage('Check Apache logs for errors') {
            steps {
                sshagent(credentials: ['jenkins-ssh-jenkinskey']) {
                    sh '''
                    ssh $REMOTE_USER@$REMOTE_HOST "
                        echo 'Checking for 4xx errors in access.log:'
                        sudo grep ' 4[0-9][0-9] ' /var/log/apache2/access.log || echo 'No 4xx errors found.'

                        echo 'Checking for 5xx errors in error.log:'
                        sudo grep ' 5[0-9][0-9] ' /var/log/apache2/error.log || echo 'No 5xx errors found.'
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
