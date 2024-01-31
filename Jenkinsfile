// if (currentBuild.getBuildCauses().toString().contains('BranchIndexingCause')) {
//     print "INFO: Build skipped due to trigger being Branch Indexing"
//     currentBuild.result = 'ABORTED' // optional, gives a better hint to the user that it's been skipped, rather than the default which shows it's successful
//     return
// }
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
                        if(WEB_SERVER_LIST.contains("116.118.95.121")){
                            def result = powershell(
                            returnStdout: true,
                            script: """
                                \$securepassword = ConvertTo-SecureString -String 'As@19006123' -AsPlainText -Force
                                \$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList 'stewie12061', \$securepassword
                                \$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
                                \$session = New-PSSession -ConnectionUri "https://116.118.95.121:5986" -Credential \$cred -SessionOption \$sessionOption
                                Invoke-Command -Session \$session -ScriptBlock {
                                    Get-ChildItem -Path 'D:\\ERP9' -Directory | Select-Object -ExpandProperty Name
                                }
                            """
                            ).trim()
                            def foldersList = result.tokenize('\n').collect { "\\"${it.trim()}\\"" }

                            // Parse the result and add to the customers list
                            customers.addAll(result.tokenize('\\n'))
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
                            customers.addAll(["stewie","jennie","Lisa"])
                        }
                        return customers
                    '''
                ]
            )
        ]
    ])
])

pipeline {

    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
    }

    parameters {
        string(description: 'Fill agent to run', name: 'build_agent')
        string(name: 'USERNAME', defaultValue: 'stewie12061', description: 'User login to server')
        string(name: 'PASSWORD', defaultValue: 'As@19006123', description: 'Password login to server')
        string(name: 'SOURCE_PATH', defaultValue: 'D:\\00.PUBLISH', description: 'Path to source web')
        string(name: 'DESTINATION_FOLDER', defaultValue: 'Publish-stewie', description: 'Folder Publish on Servers')
    }

    agent {
        label params['build_agent']
    }

    stages {
        stage('test'){
            steps{
                script{
                    def result = powershell(
                        returnStdout: true,
                        script: """
                            \$securepassword = ConvertTo-SecureString -String 'As@19006123' -AsPlainText -Force
                            \$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList 'stewie12061', \$securepassword
                            \$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
                            \$session = New-PSSession -ConnectionUri "https://116.118.95.121:5986" -Credential \$cred -SessionOption \$sessionOption
                            Invoke-Command -Session \$session -ScriptBlock {
                                Get-ChildItem -Path 'D:\\ERP9' -Directory | Select-Object -ExpandProperty Name
                            }
                        """
                    ).trim()
                    def foldersList = result.tokenize('\n').collect { "\"${it.trim()}\"" }

                    echo "$foldersList"
                }
            }
        }
        // stage('Zip Folder Publish'){
        //     steps{
        //         script{
        //             //zip folder to jenkins workspace
        //             def zipPublish = '''
        //                 $source = "$env:SOURCE_PATH"
        //                 Compress-Archive -Path "$source\\*" -DestinationPath "PUBLISH.zip" -Force
        //             '''
        //             powershell(script: zipPublish)
        //         }
        //     }
        // }
        // stage('Create Folder Publish on Server'){
        //     steps{
        //         script{
        //             String[] webServers = params.WEB_SERVER_LIST.split(',')
        //             def username = "${env:USERNAME}"
        //             def password = "${env:PASSWORD}"
        //             def desFolder = "${env:DESTINATION_FOLDER}"
        //             def builders = [:]
        //             for(webServer in webServers){
        //                 def remotePSSession = """
        //                     Write-Host "Web Server: ${webServer}"
        //                     \$uri = "https://${webServer}:5986"
        //                     \$securepassword = ConvertTo-SecureString -String '${password}' -AsPlainText -Force
        //                     \$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList '${username}', \$securepassword

        //                     \$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
        //                     \$session = New-PSSession -ConnectionUri \$uri -Credential \$cred -SessionOption \$sessionOption
        //                     Invoke-Command -Session \$session -ScriptBlock {
        //                         if (-not (Test-Path "D:\\Publish\\${desFolder}" -PathType Container)) {
        //                             # If the folder doesn't exist, create it
        //                             New-Item -ItemType Directory -Path "D:\\Publish\\${desFolder}" -Force
        //                             Write-Host "Folder created: "D:\\Publish\\${desFolder}" "
        //                         }
        //                     }
        //                     Remove-PSSession \$session
        //                 """

        //                 builders[webServer] = {
        //                     powershell(script: remotePSSession)
        //                 }
        //             }
        //             parallel builders
                    
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
        //                 def desFolder = "${env:DESTINATION_FOLDER}"

        //                 def copyscript = """
        //                     # Connect to the network drive
        //                     net use $drive: \\\\$webServer\\Publish\\$desFolder /user:$remoteName\\$username $password

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
        //             def desFolder = "${env:DESTINATION_FOLDER}"
        //             def builders = [:]
        //             for(webServer in webServers){
        //                 def remotePSSession = """
        //                     Write-Host "Web Server: ${webServer}"
        //                     \$uri = "https://${webServer}:5986"
        //                     \$securepassword = ConvertTo-SecureString -String '${password}' -AsPlainText -Force
        //                     \$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList '${username}', \$securepassword

        //                     \$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
        //                     \$session = New-PSSession -ConnectionUri \$uri -Credential \$cred -SessionOption \$sessionOption
        //                     Invoke-Command -Session \$session -ScriptBlock {
        //                         Microsoft.PowerShell.Archive\\Expand-Archive -Path "D:\\Publish\\${desFolder}\\PUBLISH.zip" -DestinationPath "D:\\Publish\\${desFolder}\\PUBLISH" -Force
        //                     }
        //                     Remove-PSSession \$session
        //                 """

        //                 builders[webServer] = {
        //                     powershell(script: remotePSSession)
        //                 }
        //             }
        //             parallel builders
                    
        //         }
        //     }
        // }
        // stage('Sync Publish folder'){
        //     steps{
        //         script{
        //             String[] webServers = params.WEB_SERVER_LIST.split(',')
        //             def username = "${env:USERNAME}"
        //             def password = "${env:PASSWORD}"
        //             def desFolder = "${env:DESTINATION_FOLDER}"
        //             def builders = [:]
        //             for(webServer in webServers){
        //                 def customer = "SERVER_${webServer}_CUSTOMER_LIST"
        //                 def customers = params."${customer}"

        //                 def remotePSSession = """
        //                     \$customersList = "${customers}"
        //                     Write-Host "Web Server: ${webServer}, Customers: \$customersList"
        //                     \$uri = "https://${webServer}:5986"
        //                     \$securepassword = ConvertTo-SecureString -String '${password}' -AsPlainText -Force
        //                     \$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList '${username}', \$securepassword

        //                     \$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
        //                     \$session = New-PSSession -ConnectionUri \$uri -Credential \$cred -SessionOption \$sessionOption

        //                     Invoke-Command -Session \$session -ScriptBlock {
        //                         param(\$innerCustomersList)
        //                         foreach (\$customer in \$innerCustomersList -split ',') {
        //                             robocopy "D:\\Publish\\${desFolder}\\PUBLISH" "D:\\ERP9\\\$customer\\Web" /E /MIR /XD "Attached Logs" /XF web.config /MT:4 /NP /NDL /NFL /NC /NS
        //                         }
                                
        //                     } -ArgumentList \$customersList
        //                     Remove-PSSession \$session
        //                 """
        //                 builders[webServer] = {
        //                     powershell(script: remotePSSession)
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


