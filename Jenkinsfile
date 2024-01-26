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
                    script: 'return ["116.118.95.121:selected","103.245.249.218:selected","10.0.0.1"]'
                ]
            )
        ],
        [$class: 'CascadeChoiceParameter',
            choiceType: 'PT_CHECKBOX', 
            description: 'Select customer to update', 
            filterLength: 1, 
            filterable: false, 
            name: 'CUSTOMER_LIST', 
            randomName: 'choice-parameter-370758416905301',
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
                        def customers = []
                        if(WEB_SERVER_LIST.contains("116.118.95.121")){
                            customers.addAll(["KH-ERP8-01","KH-ERP8-02","KH-ERP9-01","KH-ERP9-02:selected","KH-ERP9-03:selected"])
                        }
                        if(WEB_SERVER_LIST.contains("103.245.249.218")){
                            customers.addAll(["KH-ERP8-03","KH-ERP8-04","KH-ERP9-04:selected","KH-ERP9-05","KH-ERP9-06:selected"])
                        }
                        if(WEB_SERVER_LIST.contains("10.0.0.1")){
                            customers.addAll(["stewie","jennie"])
                        }
                        return customers
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
                    def customers = params.CUSTOMER_LIST.split(',').collect { it.trim() }
                    echo "$webServers"
                    echo "$customers"
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


