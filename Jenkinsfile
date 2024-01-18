pipeline {
    agent any

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
    }

    environment {
        ANSIBLE_CRED = credentials('9940612d-bc87-4df5-b041-1436dae725c4')
        ANSIBLE_USERNAME = ANSIBLE_CRED_USR
        ANSIBLE_PASSWORD = ANSIBLE_CRED_PSW
    }

    parameters {
        string(name: 'WEB_SERVER_LIST', defaultValue: '', description: 'Comma-separated list of web servers')
    }

    stages {
        stage('Generate Inventory File') {
            steps {
                script {
                    def webServers = params.WEB_SERVER_LIST.split(',')
                    def inventoryContent = "[win]\n${webServers.join('\n')}\n\n[win:vars]\nansible_user=${ANSIBLE_USERNAME}\nansible_password=${ANSIBLE_PASSWORD}\nansible_port=5986\nansible_connection=winrm\nansible_winrm_server_cert_validation=ignore"
                    writeFile file: 'hosts.ini', text: inventoryContent
                }
            }
        }

        stage('Run Ansible Playbook update WEB') {
            steps {
                ansiblePlaybook(
                    playbook: 'update_publish.yml',
                    inventory: 'hosts.ini',
                )
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
