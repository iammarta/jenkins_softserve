pipeline {
    agent any

    environment {
        REMOTE_USER = 'iammarta'            // Remote VM username
        REMOTE_HOST = '192.168.64.2'        // Remote VM IP address
        SSH_CREDENTIALS_ID = 'jenkins-ssh-jenkinskey'  // Jenkins SSH credential ID
    }

    stages {
        stage('Install Apache on Remote VM') {
            steps {
                sshagent(credentials: [env.SSH_CREDENTIALS_ID]) {
                    script {
                        // Run update, install Apache2, start and enable service on remote VM
                        def installCmd = """
                            sudo apt update &&
                            sudo apt install -y apache2 &&
                            sudo systemctl start apache2 &&
                            sudo systemctl enable apache2
                        """
                        sh "ssh -o StrictHostKeyChecking=no ${env.REMOTE_USER}@${env.REMOTE_HOST} '${installCmd}'"
                    }
                }
            }
        }

        stage('Check Apache status') {
            steps {
                sshagent(credentials: [env.SSH_CREDENTIALS_ID]) {
                    script {
                        // Check if Apache service is running
                        def statusCmd = "sudo systemctl status apache2 | grep Active"
                        sh "ssh ${env.REMOTE_USER}@${env.REMOTE_HOST} '${statusCmd}'"
                    }
                }
            }
        }

        stage('Check Apache logs for errors') {
            steps {
                sshagent(credentials: [env.SSH_CREDENTIALS_ID]) {
                    script {
                        // Search for HTTP 4xx and 5xx errors in Apache logs
                        def logCheckCmd = '''
                            echo "Checking for 4xx errors in access.log:"
                            sudo grep " 4[0-9][0-9] " /var/log/apache2/access.log || echo "No 4xx errors found."

                            echo "Checking for 5xx errors in error.log:"
                            sudo grep " 5[0-9][0-9] " /var/log/apache2/error.log || echo "No 5xx errors found."
                        '''
                        sh "ssh ${env.REMOTE_USER}@${env.REMOTE_HOST} '${logCheckCmd}'"
                    }
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
