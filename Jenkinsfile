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
                            customers.addAll(["KH-ERP9-01","KH-ERP9-02:selected","KH-ERP9-03:selected"])
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
                            customers.addAll(["KH-ERP9-04:selected","KH-ERP9-05","KH-ERP9-06:selected"])
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
    }

    stages {
        // stage('Zip Folder Publish'){
        //     steps{
        //         script{
        //             def zipPublish = '''
        //                 $source = "$env:SOURCE_PATH"
        //                 Compress-Archive -Path "$source\\*" -DestinationPath "PUBLISH.zip" -Force
        //             '''
        //             powershell(script: zipPublish)
        //         }
        //     }
        // }
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
        //                     net use $drive: \\\\$webServer\\Publish /user:$remoteName\\$username $password

        //                     # Execute robocopy and wait for it to finish
        //                     Start-Process robocopy -ArgumentList @(
        //                         "${env:WORKSPACE}",
        //                         "$drive:\\",
        //                         "PUBLISH.zip",
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
        // stage('Unzip Folder Publish'){
        //     steps{
        //         script{
        //             String[] webServers = params.WEB_SERVER_LIST.split(',')
        //             def username = "${env:USERNAME}"
        //             def password = "${env:PASSWORD}"
        //             def builders = [:]
        //             for(webServer in webServers){
        //                 def remotePSSession = """
        //                     $uri = "https://$webServer:5986"
        //                     $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
        //                     $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $username, $securepassword

        //                     $sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
        //                     $session = New-PSSession -ConnectionUri $uri -Credential $cred -SessionOption $sessionOption
        //                     Invoke-Command -Session $session -ScriptBlock {
        //                         Expand-Archive -Path "D:\\Publish\\PUBLISH.zip" -DestinationPath "D:\\Publish\\PUBLISH" -Force
        //                     }
        //                     Remove-PSSession $session
        //                 """

        //                 builders[webServer] = {
        //                     powershell(script: remotePSSession)
        //                 }
        //             }
        //             parallel builders
                    
        //         }
        //     }
        // }
        stage('Sync Publish folder'){
            steps{
                script{
                    String[] webServers = params.WEB_SERVER_LIST.split(',')
                    def username = "${env:USERNAME}"
                    def password = "${env:PASSWORD}"
                    def builders = [:]
                    for(webServer in webServers){
                        def customer = "SERVER_${webServer}_CUSTOMER_LIST"
                        String[] customers = params."${customer}".split(',')
                        def remotePSSession = """
                            \$uri = "https://${webServer}:5986"
                            \$securepassword = ConvertTo-SecureString -String '${password}' -AsPlainText -Force
                            \$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList '${username}', \$securepassword

                            \$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
                            \$session = New-PSSession -ConnectionUri \$uri -Credential \$cred -SessionOption \$sessionOption

                            Invoke-Command -Session \$session -ScriptBlock {
                                # Get folder names
                                \$webSubfolders = Get-ChildItem -Path "D:\\ERP9" -Directory | ForEach-Object {
                                    \$folderName = \$_.Name
                                    if (\$folderName -in '${customers}') {
                                        \$folderName
                                    }
                                }
                                \$webSubfolders
                            }
                            Remove-PSSession \$session
                        """

                        builders[webServer] = {
                            powershell(script: remotePSSession)
                        }
                    }
                    parallel builders
                    
                }
            }
        }
    }


    post {
        always {
            echo 'Finished'
            cleanWs()
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


