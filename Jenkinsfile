pipeline {
    agent any

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
    }

    environment {
        WEB_SERVER_IP = '116.118.95.121'
        WEB_SERVER_2_IP = '103.245.249.218'
        WEBSERVER_PASSWORD = credentials('web-server-password-creds')
        WEBSERVER_USERNAME = 'stewie12061'
        ANSIBLE_PATH = 'D:/Ansible'
    }

    stages {
        stage('Build') {
            steps {
                git 'https://github.com/jglick/simple-maven-project-with-tests.git'
                script {
                    def selectedHost = input message: 'Please select the host', ok: 'Next',
                                        parameters: [
                                        choice(name: 'PRODUCT', choices: getHostList("win","D:\\UpdateSourceWeb\\hosts.ini"), description: 'Please select the host')]
                    
                    echo "Host:::: $selectedHost"
                }
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
def getHostList(def appName, def filePath) {

    def hosts = []
    def content = readFile(file: filePath)
    def startCollect = false
    for(def line : content.split('\n')) {
        if(line.contains("["+ appName +"]")){ // This is a starting point of host entries
            startCollect = true
            continue
        } else if(startCollect) {
            if(!line.allWhitespace && !line.contains('[')){
                hosts.add(line.trim())
            } else {
                break
            }
        } 
    }
    return hosts
}