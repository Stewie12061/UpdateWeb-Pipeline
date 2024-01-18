pipeline {
    agent any

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
    }

    stages {
        stage('Run Ansible Playbook update WEB') {
            steps {
                powershell(script: 'wsl ansible-playbook -i hosts.ini update_publish.yml')
            }
        }
    }

    post {
        always {
            echo 'Finished'
        }
        success {
            echo 'Succeeeded!'
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