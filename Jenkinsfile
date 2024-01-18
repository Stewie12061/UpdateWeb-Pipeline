pipeline {
    agent any

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
    }

    environment{
        WEBSERVER_PASSWORD = credentials('web-server-password-creds')
    }

    parameters {
        string(name: 'WEB_SERVER_LIST', defaultValue: '', description: 'Comma-separated list of web servers')
    }

    stages {
        stage('Generate Inventory File') {
            steps {
                script {
                    def webServers = params.WEB_SERVER_LIST.split(',')
                    def inventoryContent = "[win]\n${webServers.join('\n')}\n\n[win:vars]\nansible_user=${params.WEBSERVER_USERNAME}\nansible_password=${WEBSERVER_PASSWORD}\nansible_port=5986\nansible_connection=winrm\nansible_winrm_server_cert_validation=ignore"
                    writeFile file: 'hosts.ini', text: inventoryContent
                }
            }
        }

        stage('Run Ansible Playbook update WEB') {
            steps {
                ansiblePlaybook(
                    playbook: 'update_publish.yml',
                    inventory: 'hosts.ini',
                    credentialsId: 'web-server-password-creds'
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
