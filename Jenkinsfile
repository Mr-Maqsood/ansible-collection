pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Mr-Maqsood/ansible-collection.git'
            }
        }

        // ← Ye stage sabse pehle Galaxy ke 500 error ko hamesha ke liye khatam kar dega
        stage('Install Collections') {
            steps {
                retry(4) {                    // 4 baar try karega agar Galaxy down ho
                    timeout(time: 2, unit: 'MINUTES') {
                        sh 'ansible-galaxy collection install -r requirements.yml --force'
                    }
                }
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
                        chmod 600 "$SSH_KEY"   # Jenkins aksar khud kar deta hai, phir bhi safe

                        echo "Running Ansible Playbook on real servers..."
                        ansible-playbook -i inventory.ini site.yml \
                            --private-key "$SSH_KEY" \
                            --vault-password-file vault_pass.txt \
                            --ssh-common-args="-o StrictHostKeyChecking=no"   # pehli baar host key accept kar lega

                        rm -f vault_pass.txt
                    '''
                }
            }
        }
    }

    // ← Global timeout + failure pe bhi email ya notification aa sake
    options {
        timeout(time: 20, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
}
