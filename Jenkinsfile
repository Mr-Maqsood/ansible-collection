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
                echo "Ansible Galaxy down hai — temporarily skip kiya gaya hai"
                // jab Galaxy theek ho jayega tab ye line wapas enable kar dena
                // sh 'ansible-galaxy collection install -r requirements.yml --force'
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

                        echo "=== Playbook chal raha hai real servers pe ==="
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

    options {
        timeout(time: 15, unit: 'MINUTES')
    }
}
