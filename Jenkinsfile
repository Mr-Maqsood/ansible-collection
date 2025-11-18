stage('Run Playbook') {
    steps {
        withCredentials([
            string(credentialsId: 'vault-pass', variable: 'ANSIBLE_VAULT_PASS'),
            sshUserPrivateKey(credentialsId: 'ssh-key', keyFileVariable: 'SSH_KEY')
        ]) {
            sh '''
                echo "$ANSIBLE_VAULT_PASS" > vault_pass.txt

                # Yeh line zaroori hai — Jenkins wala key 0600 bana dega
                chmod 600 "$SSH_KEY"

                echo "Running playbook on real servers..."
                ansible-playbook -i inventory.ini site.yml \
                    --private-key "$SSH_KEY" \
                    --vault-password-file vault_pass.txt \
                    -vvv

                rm -f vault_pass.txt
            '''
        }
    }
}
