pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/Mr-Maqsood/ansible-collection.git'
            }
        }

        stage('Install Collections') {
            steps {
                sh 'ansible-galaxy collection install -r requirements.yml --force'
            }
        }

        stage('Run Playbook') {
            steps {
                withCredentials([
                    string(credentialsId: 'vault-pass', variable: 'ANSIBLE_VAULT_PASS'),
                    sshUserPrivateKey(credentialsId: 'ssh-key', keyFileVariable: 'SSH_KEY')
                ]) {
                    sh '''
                        echo "$ANSIBLE_VAULT_PASS" > vault_pass.txt
                        chmod 600 "$SSH_KEY"
                        
                        ansible-playbook -i inventory.ini site.yml \
                            --private-key "$SSH_KEY" \
                            --vault-password-file vault_pass.txt
                        
                        rm -f vault_pass.txt
                    '''
                }
            }
        }
    }
}
