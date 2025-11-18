


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
                withCredentials([
                    string(credentialsId: 'vault-pass', variable: 'ANSIBLE_VAULT_PASS'),
                    file(credentialsId: 'ssh-pem-key', variable: 'SSH_KEY')
                ]) {
                    sh '''
                        echo "$ANSIBLE_VAULT_PASS" > vault_pass.txt

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
