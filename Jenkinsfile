// if (currentBuild.getBuildCauses().toString().contains('BranchIndexingCause')) {
//     print "INFO: Build skipped due to trigger being Branch Indexing"
//     currentBuild.result = 'ABORTED' // optional, gives a better hint to the user that it's been skipped, rather than the default which shows it's successful
//     return
// }
def customers_list = []
node('master') {
    stage('prepare choices') {
        def my_choices = powershell(script: 'Get-ChildItem D:\\00.PUBLISH -Directory | Select-Object -ExpandProperty Name', returnStdout: true)

        customers_list = my_choices.split("\n").collect { "\"${it.trim()}:selected\"" }

        echo "$customers_list"
    }
}


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
                    script: 'return ["116.118.95.121:selected","103.245.249.218:selected"]'
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
                    script: 'return ["error:disabled"]'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false,
                    script: 'return ${customers_list}'
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
                            customers.addAll(["KH-ERP9-04","KH-ERP9-05:selected","KH-ERP9-06:selected","KH-ERP9-08:selected"])
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
        choice(choices: "${customers_list}", name: 'DEPLOYMENT_ENVIRONMENT', description: 'please choose the environment you want to deploy?')
    }

    agent {
        label params['build_agent']
    }

    stages {
        stage('Zip Folder Publish'){
            steps{
                script{
                    //zip folder to jenkins workspace
                    def zipPublish = '''
                        $source = "$env:SOURCE_PATH"
                        Compress-Archive -Path "$source\\*" -DestinationPath "PUBLISH.zip" -Force
                    '''
                    powershell(script: zipPublish)
                }
            }
        }
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
        stage('Push source to Server') {
            steps {
                script {
                    String[] webServers = params.WEB_SERVER_LIST.split(',')
                    def builders = [:]
                    for(webServer in webServers){

                        def username = "${env:USERNAME}"
                        def password = "${env:PASSWORD}"
                        def desFolder = "${env:DESTINATION_FOLDER}"

                        def copyscript = """
                            \$uri = "https://${webServer}:5986"
                            \$securepassword = ConvertTo-SecureString -String '${password}' -AsPlainText -Force
                            \$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList '${username}', \$securepassword

                            \$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
                            \$session = New-PSSession -ConnectionUri \$uri -Credential \$cred -SessionOption \$sessionOption

                            Copy-Item -Path "${env:WORKSPACE}\\PUBLISH.zip" -Destination "D:\\Publish\\${desFolder}" -ToSession \$session -Force

                            Remove-PSSession \$session
                        """

                        builders[webServer] = {
                            powershell(script: "$copyscript")
                        }
                    }
                    parallel builders
                }
            }
        }
        stage('Unzip Folder Publish'){
            steps{
                script{
                    String[] webServers = params.WEB_SERVER_LIST.split(',')
                    def username = "${env:USERNAME}"
                    def password = "${env:PASSWORD}"
                    def desFolder = "${env:DESTINATION_FOLDER}"
                    def builders = [:]
                    for(webServer in webServers){
                        def remotePSSession = """
                            Write-Host "Web Server: ${webServer}"
                            \$uri = "https://${webServer}:5986"
                            \$securepassword = ConvertTo-SecureString -String '${password}' -AsPlainText -Force
                            \$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList '${username}', \$securepassword

                            \$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
                            \$session = New-PSSession -ConnectionUri \$uri -Credential \$cred -SessionOption \$sessionOption
                            Invoke-Command -Session \$session -ScriptBlock {
                                Microsoft.PowerShell.Archive\\Expand-Archive -Path "D:\\Publish\\${desFolder}\\PUBLISH.zip" -DestinationPath "D:\\Publish\\${desFolder}\\PUBLISH" -Force
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
        stage('Sync Publish folder'){
            steps{
                script{
                    String[] webServers = params.WEB_SERVER_LIST.split(',')
                    def username = "${env:USERNAME}"
                    def password = "${env:PASSWORD}"
                    def desFolder = "${env:DESTINATION_FOLDER}"
                    def builders = [:]
                    for(webServer in webServers){
                        def customer = "SERVER_${webServer}_CUSTOMER_LIST"
                        def customers = params."${customer}"

                        def remotePSSession = """
                            \$customersList = "${customers}"
                            Write-Host "Web Server: ${webServer}, Customers: \$customersList"
                            \$uri = "https://${webServer}:5986"
                            \$securepassword = ConvertTo-SecureString -String '${password}' -AsPlainText -Force
                            \$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList '${username}', \$securepassword

                            \$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
                            \$session = New-PSSession -ConnectionUri \$uri -Credential \$cred -SessionOption \$sessionOption

                            Invoke-Command -Session \$session -ScriptBlock {
                                param(\$innerCustomersList)
                                foreach (\$customer in \$innerCustomersList -split ',') {
                                    robocopy "D:\\Publish\\${desFolder}\\PUBLISH" "D:\\ERP9\\\$customer\\Web" /E /MIR /XD "Attached Logs" /XF web.config /MT:4 /NP /NDL /NFL /NC /NS
                                }
                                \$FolderPath = "D:\\Publish\\${desFolder}"
                                #Check if folder exists
                                If (Test-Path \$FolderPath) {
                                    # Folder not exist, delete it!
                                    Remove-Item -Path \$FolderPath -Recurse
                                    Write-host "Clean up ${desFolder}" -f Green
                                }
                                Else {
                                    Write-host "Folder ${desFolder} does not exists!" -f Red
                                }
                                
                            } -ArgumentList \$customersList
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


