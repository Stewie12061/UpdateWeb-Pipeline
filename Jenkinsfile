pipeline {
    agent any

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
    }

    parameters {
        choice(name: 'WEB_SERVER_LIST', choices: '116.118.95.121,103.245.249.218', description: 'Select WEB Servers', defaultValue: '116.118.95.121,103.245.249.218', multiSelectDelimiter: ',')
    }

    stages {
        stage('Generate Inventory File') {
            steps {
                script {
                    def webServers = params.WEB_SERVER_LIST
                    echo "Selected web servers: ${webServers}"

                    // Use the 'webServers' variable in your further processing
                    // For example, you can use it to generate the inventory file.
                    def inventoryContent = "[win]\n${webServers.join('\n')}\n\n[win:vars]\nansible_user=${ANSIBLE_CRED_USR}\nansible_password=${ANSIBLE_CRED_PSW}\nansible_port=5986\nansible_connection=winrm\nansible_winrm_server_cert_validation=ignore"
                    writeFile file: 'hosts.ini', text: inventoryContent
                }
            }
        }
    }

    post {
        always {
            echo 'Finished'
        }
        success {
            echo 'Succeeded!'
        }
        unstable {
            echo 'Unstable :/'
        }
        failure {
            echo 'Failed :('
        }
        changed {
            echo 'Things were different before...'
        }
    }
}
