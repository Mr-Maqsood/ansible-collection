pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Mr-Maqsood/ansible-collection.git'
            }
        }

        stage('Install Collections') {
            steps {
                sh 'ansible-galaxy collection install -r requirements.yml'
            }
        }

        stage('Run Playbook') {
            steps {
                // Use Jenkins credentials: Vault password + uploaded PEM key
                withCredentials([
                    string(credentialsId: 'ansible-vault-pass', variable: 'ANSIBLE_VAULT_PASS'),
                    file(credentialsId: 'ansible-vault-pass', variable: 'SSH_KEY')
                ]) {
                    sh '''
                        # Create temporary file for Vault password
                        echo "$ANSIBLE_VAULT_PASS" > vault_pass.txt

                        # Run Ansible playbook using the uploaded PEM key
                        ansible-playbook -i inventory.ini site.yml \
                          --private-key $SSH_KEY \
                          --vault-password-file vault_pass.txt

                        # Cleanup temporary vault password file
                        rm -f vault_pass.txt
                    '''
                }
            }
        }
    }
}


