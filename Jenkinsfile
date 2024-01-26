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
            name: 'SERVER_116.118.95.121_CUSTOMER_LIST', 
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
                        return customers
                    '''
                ]
            )
        ],
        [$class: 'CascadeChoiceParameter',
            choiceType: 'PT_CHECKBOX', 
            description: 'Select customer to update', 
            filterLength: 1, 
            filterable: false, 
            name: 'SERVER_103.245.249.218_CUSTOMER_LIST', 
            randomName: 'choice-parameter-370758416905302',
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
                        if(WEB_SERVER_LIST.contains("103.245.249.218")){
                            customers.addAll(["KH-ERP8-03","KH-ERP8-04","KH-ERP9-04:selected","KH-ERP9-05","KH-ERP9-06:selected"])
                        }
                        return customers
                    '''
                ]
            )
        ],
        [$class: 'CascadeChoiceParameter',
            choiceType: 'PT_CHECKBOX', 
            description: 'Select customer to update', 
            filterLength: 1, 
            filterable: false, 
            name: 'SERVER_10.0.0.1_CUSTOMER_LIST', 
            randomName: 'choice-parameter-370758416905303',
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

    parameters {
        string(name: 'USERNAME', defaultValue: 'stewie12061', description: 'User login to server')
        string(name: 'PASSWORD', defaultValue: 'As@19006123', description: 'Password login to server')
        string(name: 'SOURCE_PATH', defaultValue: 'D:\\00.PUBLISH', description: 'Path to source web')
        string(name: 'DESTINATION_PATH', defaultValue: 'D:\\', description: 'Path to destination server')
    }

    stages {
        stage('Push source to Server') {
            steps {
                script {
                    def webServers = params.WEB_SERVER_LIST.split(',')
                    def builders = [:]
                    for(webServer in webServers){
                        builders[webServer] = {
                            stage("Push source web to Server ${webServer}") {
                                def zipPublish = '''
                                    $source = "$env:SOURCE_PATH"
                                    Compress-Archive -Path "$source\\*" -DestinationPath "$source.zip" -Force
                                '''
                                powershell(script: zipPublish)
                                // remote = [:]
                                // remote.host = "${webServer}"
                                // remote.allowAnyHosts = true
                                // remote.failOnError = true
                                // remote.user = "${env:USERNAME}"
                                // remote.password = "${env:PASSWORD}"

                                // sshPut remote: remote, from: "${env:SOURCE_PATH}", into: "${env:DESTINATION_PATH_PATH}"
                            }
                        }
                    }
                    parallel builders
                }
            }
        }
        // stage('Update source web to server') {
        //     steps {
        //         script {
        //             def webServers = params.WEB_SERVER_LIST.split(',')
        //             def builders = [:]
        //             for(webServer in webServers){
        //                 builders[webServer] = {
        //                     stage("Update web on Server ${webServer}") {
        //                         echo "Running stage for Server ${webServer}"
        //                         def customers = params."SERVER_${webServer}_CUSTOMER_LIST".split(',').collect { it.trim() }
        //                     }
        //                 }
        //             }
        //             parallel builders
        //         }
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


