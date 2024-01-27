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
        string(name: 'DESTINATION_NAME', defaultValue: 'Publish', description: 'Folder publish name shared on remote server')
    }

    stages {
        state('Zip Folder Publish'){
            steps{
                script{
                    def zipPublish = '''
                        $source = "$env:SOURCE_PATH"
                        Compress-Archive -Path "$source\\*" -DestinationPath "PUBLISH.zip" -Force
                    '''
                    powershell(script: zipPublish)
                }
            }
        }
        // stage('Push source to Server') {
        //     steps {
        //         script {
        //             String[] webServers = params.WEB_SERVER_LIST.split(',')
        //             def builders = [:]
        //             for(webServer in webServers){
        //                 echo "$webServer"
        //                 def remoteName = ""
        //                 def drive = ""
        //                 if(webServer.equals("116.118.95.121")){
        //                     remoteName = "web-server"
        //                     drive = "Z"
        //                 }
        //                 if(webServer.equals("103.245.249.218")){
        //                     remoteName = "web-server-2"
        //                     drive = "Y"
        //                 }
        //                 if(webServer.equals("10.0.0.1")){
        //                     remoteName = "test"
        //                     drive = "X"
        //                 }
        //                 def username = "${env:USERNAME}"
        //                 def password = "${env:PASSWORD}"

        //                 def copyscript = """
        //                     # Connect to the network drive
        //                     net use $drive: \\\\$webServer\\${env:DESTINATION_NAME} /user:$remoteName\\$username $password

        //                     # Execute robocopy and wait for it to finish
        //                     Start-Process robocopy -ArgumentList @(
        //                         "${env:SOURCE_PATH}",
        //                         "$drive:\\",
        //                         "PUBLISH.zip",
        //                         "/E",
        //                         "/MT:8",
        //                         "/np",
        //                         "/ndl",
        //                         "/nfl",
        //                         "/nc",
        //                         "/ns"
        //                     ) -Wait

        //                     # Disconnect from the network drive
        //                     net use $drive: /delete
        //                 """

        //                 builders[webServer] = {
        //                     powershell(script: "$copyscript")
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


