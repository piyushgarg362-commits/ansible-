
pipeline {
    agent any

    options {
        timestamps()
    }

    environment {
        REPO_URL = 'https://github.com/Bringways/ansible-.git'
        ANSIBLE_HOST_KEY_CHECKING = 'False'
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Bringways/ansible-.git'
            }
        }

        stage('Validate Files') {
            steps {
                sh '''
                    test -f index.html
                    test -f inventory
                    test -f deploy.yml

                    echo "All project files found."
                '''
            }
        }

stage('Ansible Syntax Check') {
    steps {
        sh '''
            echo "Checking Ansible installation..."
            /home/ansible/.local/bin/ansible-playbook --version

            echo "Running syntax check..."
            /home/ansible/.local/bin/ansible-playbook -i inventory deploy.yml --syntax-check
        '''
    }
}

        stage('Deploy to EC2') {
            steps {
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'ec2-ssh-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    sh '''
                        ansible-playbook \
                            -i inventory \
                            deploy.yml \
                            --private-key "$SSH_KEY" \
                            -u "$SSH_USER"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Website deployed using Jenkins and Ansible!'
        }

        failure {
            echo 'FAILED: Check Jenkins Console Output.'
        }

        always {
            echo 'Pipeline execution completed.'
        }
    }
}
