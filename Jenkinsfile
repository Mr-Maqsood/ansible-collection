pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Mr-Maqsood/ansible-collection.git'
            }
        }

        // Galaxy down hai — stage skip kar diya
        stage('Install Collections') {
            steps {
                echo "Ansible Galaxy is down globally today — skipping collection install"
                echo "Will re-enable when galaxy.ansible.com is back"
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

                        echo "Running playbook (without collections for now)..."
                        ansible-playbook -i inventory.ini site.yml \
                            --private-key "$SSH_KEY" \
                            --vault-password-file vault_pass.txt \
                            -vvv

                        rm -f vault_pass.txt
                    '''
                }
            }
        }
    }
}
