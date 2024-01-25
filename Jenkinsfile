properties([
    parameters([
        [$class: 'ChoiceParameter',
            choiceType: 'PT_CHECKBOX', 
            description: 'Select Web Server to update source web', 
            filterLength: 1, 
            filterable: false, 
            name: 'WEB_SERVER_LIST', 
            randomName: 'choice-parameter-370758416905300', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                        'return[\'Could not get Env\']'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: '''
                        return [
                            "116.118.95.121:selected",
                            "103.245.249.218:selected",
                            "10.0.0.1"
                        ]
                    '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_CHECKBOX', 
            description: 'Select Options', 
            filterLength: 1, 
            filterable: false, 
            name: 'OPTIONS_LIST', 
            randomName: 'choice-parameter-370758416905301', 
            referencedParameters: 'WEB_SERVER_LIST',
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                        'return[\'Could not get Environment from Env Param\']'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                        ''' if (WEB_SERVER_LIST.equals("116.118.95.121")){
                                return["devaaa001","devaaa002","devbbb001","devbbb002","devccc001","devccc002"]
                            }
                            else if(WEB_SERVER_LIST.equals("103.245.249.218")){
                                return["qaaaa001","qabbb002","qaccc003"]
                            }
                            else if(WEB_SERVER_LIST.equals("10.0.0.1")){
                                return["staaa001","stbbb002","stccc003"]
                            }
                        '''
                ]
            ]
        ]
    ])
])

pipeline {
    agent any

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
    }

    // environment {
    //     ANSIBLE_CRED = credentials('9940612d-bc87-4df5-b041-1436dae725c4')
    // }

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
