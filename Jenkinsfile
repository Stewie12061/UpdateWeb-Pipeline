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
                    script: 'return []'
                ], 
                script: '''
                    def optionsMap = [:]

                    def executeCommand(server, command) {
                        def powershellScript = """
                            \$uri = "https://$server:5986"
                            \$user = "stewie12061"
                            \$password = "As@19006123"
                            \$securepassword = ConvertTo-SecureString -String \$password -AsPlainText -Force
                            \$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList \$user, \$securepassword

                            \$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
                            \$session = New-PSSession -ConnectionUri \$uri -Credential \$cred -SessionOption \$sessionOption
                            Invoke-Command -Session \$session -ScriptBlock {
                                $command
                            }
                            Remove-PSSession -Session \$session
                        """
                        def processBuilder = new ProcessBuilder('powershell.exe', '-Command', powershellScript)
                        def process = processBuilder.start()
                        process.waitFor()
                        return process.text.trim()
                    }

                    WEB_SERVER_LIST.each { server ->
                        if (server.toBoolean()) {
                            def result = executeCommand(server, """
                                # Connect to the server and retrieve folder names
                                # Example: Change 'D:\\ERP9' to the actual path
                                cd D:\\ERP9
                                Get-ChildItem -Directory | ForEach-Object { \$_.Name }
                            """)
                            def folderNames = result.split('\n')
                            optionsMap[server] = folderNames
                        }
                    }

                    return optionsMap[WEB_SERVER_LIST.find { it.toBoolean() }] ?: []

                '''
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
                    echo webServers
                    // def inventoryContent = "[win]\n${webServers.join('\n')}\n\n[win:vars]\nansible_user=${ANSIBLE_CRED_USR}\nansible_password=${ANSIBLE_CRED_PSW}\nansible_port=5986\nansible_connection=winrm\nansible_winrm_server_cert_validation=ignore"
                    // writeFile file: 'hosts.ini', text: inventoryContent
                }
            }
        }

        // stage('Run update WEB') {
        //     steps {
        //         powershell(script: 'wsl ansible-playbook -i hosts.ini update_publish.yml')
        //     }
        // }
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
