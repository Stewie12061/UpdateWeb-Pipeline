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
                    script: 'return ["116.118.95.121","103.245.249.218"]'
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
                    script: 'return [\'Could not get Customers\']'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false,
                    script: '''
                        
                        def powerShellScript = "Get-ChildItem -Path \\\\\\\\MSI\\\\Users\\\\test\\\\Desktop\\\\test -Name | ForEach-Object { \\\$_ + ':selected' }"
                            
                        // Execute PowerShell script
                        def command = ["powershell", "-Command", powerShellScript]
                        def proc = command.execute()
                        proc.waitFor()

                        def output = proc.in.text.trim().tokenize()

                        if(WEB_SERVER_LIST.contains("116.118.95.121")){
                            return output
                        }
                        
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
                        def powerShellScript = """\
                            \\\$securepassword = ConvertTo-SecureString -String '1' -AsPlainText -Force
                            \\\$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList 'test', \\\$securepassword
                            \\\$sessionOption = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
                            \\\$session = New-PSSession -ComputerName 'MSI' -Credential \\\$cred -SessionOption \\\$sessionOption
                            Invoke-Command -Session \\\$session -ScriptBlock {
                                Get-ChildItem -Path 'G:\\\\ASOFT\\\\ASFOT_SOURCE\\\\ASOFT_ERP_8.3.7STD_2022\\\\10.SOURCES\\\\04.SERVICES' -Name | ForEach-Object { \\\$_ + ':selected' }
                            }
                            Remove-PSSession \\\$session
                        """

                        if(WEB_SERVER_LIST.contains("103.245.249.218")){
                            // Execute PowerShell script
                            def command = ["powershell", "-Command", powerShellScript]
                            def proc = command.execute()
                            proc.waitFor()

                            def output = proc.in.text.trim().tokenize()
                            def customers = []
                            customers.addAll(output)	
                            return customers
                        }
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


