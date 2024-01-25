properties([
    parameters([
        [$class: 'ChoiceParameter',
            choiceType: 'PT_CHECKBOX', 
            description: 'Select Web Server to update source web', 
            filterLength: 1, 
            filterable: false, 
            name: 'WEB_SERVER_LIST', 
            randomName: 'choice-parameter-370758416905300', 
            script: groovyScript(
                fallbackScript: [
                    classpath: [], 
                    sandbox: false,
                    script: 'return ["error"]'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false,
                    script: 'return ["116.118.95.121", "103.245.249.218"]'
                ]
            )
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_CHECKBOX', 
            description: 'Select Options', 
            filterLength: 1, 
            filterable: false, 
            name: 'OPTIONS_LIST', 
            referencedParameters: 'WEB_SERVER_LIST',
            script: groovyScript(
                fallbackScript: [
                    classpath: [], 
                    sandbox: false,
                    script: 'return ["error"]'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false,
                    script: '''
                        def optionsMap = [:]
                        
                        def executeCommand(server, command) {
                            def processBuilder = new ProcessBuilder('powershell.exe', '-Command', command)
                            processBuilder.environment().put('SERVER', server)
                            def process = processBuilder.start()
                            process.waitFor()
                            return process.text
                        }

                        WEB_SERVER_LIST.each { server ->
                            def folderNames = executeCommand(server, '''
                                $server = $env:SERVER
                                $folders = Get-ChildItem -Path "D:\\ERP9" -Directory | Select-Object -ExpandProperty Name
                                $folders -join ','
                            ''').trim().split(',')
                            optionsMap[server] = folderNames
                        }

                        def selectedServer = WEB_SERVER_LIST.first()
                        return optionsMap.containsKey(selectedServer) ? optionsMap[selectedServer] : []
                    '''
                ]
            )
        ]
    ])
])
pipeline {
    agent any

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
    }

    environment {
        ANSIBLE_CRED = credentials('9940612d-bc87-4df5-b041-1436dae725c4')
    }

    // parameters {
    //     string(name: 'WEB_SERVER_LIST', defaultValue: '116.118.95.121, 103.245.249.218', description: 'List of WEB Server use ","')
    // }

    stages {
        stage('Generate Inventory File') {
            steps {
                script {
                    def webServers = params.WEB_SERVER_LIST.split(',').collect { it.trim() }
                    def inventoryContent = "[win]\n${webServers.join('\n')}\n\n[win:vars]\nansible_user=${ANSIBLE_CRED_USR}\nansible_password=${ANSIBLE_CRED_PSW}\nansible_port=5986\nansible_connection=winrm\nansible_winrm_server_cert_validation=ignore"
                    writeFile file: 'hosts.ini', text: inventoryContent
                }
            }
        }

        stage('Run update WEB') {
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
