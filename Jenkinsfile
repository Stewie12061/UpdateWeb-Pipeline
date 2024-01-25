// properties([
//     parameters([
//         [$class: 'CascadeChoiceParameter',
//             choiceType: 'PT_CHECKBOX', 
//             description: 'Select Web Server to update source web', 
//             filterLength: 1, 
//             filterable: false, 
//             name: 'WEB_SERVER_LIST', 
//             randomName: 'choice-parameter-370758416905300', 
//             script: [
//                 $class: 'GroovyScript', 
//                 fallbackScript: [
//                     classpath: [], 
//                     sandbox: false, 
//                     script: 
//                         'return[\'Could not get Env\']'
//                 ], 
//                 script: [
//                     classpath: [], 
//                     sandbox: false, 
//                     script: '''
//                         return [
//                             \'116.118.95.121:selected\',
//                             \'103.245.249.218:selected\',
//                             \'10.0.0.1\'
//                         ]
//                     '''
//                 ]
//             ]
//         ],
//         [$class: 'CascadeChoiceParameter', 
//             choiceType: 'PT_CHECKBOX', 
//             description: 'Select Options', 
//             filterLength: 1, 
//             filterable: false, 
//             name: 'OPTIONS_LIST', 
//             randomName: 'choice-parameter-370758416905301', 
//             referencedParameters: 'WEB_SERVER_LIST',
//             script: [
//                 $class: 'GroovyScript', 
//                 fallbackScript: [
//                     classpath: [], 
//                     sandbox: false, 
//                     script: 
//                         'return[\'Could not get Environment from Env Param\']'
//                 ], 
//                 script: [
//                     classpath: [], 
//                     sandbox: false, 
//                     script: 
//                         ''' if (WEB_SERVER_LIST.equals("116.118.95.121")){
//                                 return["devaaa001","devaaa002","devbbb001","devbbb002","devccc001","devccc002"]
//                             }
//                             else if(WEB_SERVER_LIST.equals("103.245.249.218")){
//                                 return["qaaaa001","qabbb002","qaccc003"]
//                             }
//                             else if(WEB_SERVER_LIST.equals("10.0.0.1")){
//                                 return["staaa001","stbbb002","stccc003"]
//                             }
//                         '''
//                 ]
//             ]
//         ]
//     ])
// ])

// pipeline {
//     agent any

//     options {
//         buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '5')
//     }

//     // environment {
//     //     ANSIBLE_CRED = credentials('9940612d-bc87-4df5-b041-1436dae725c4')
//     // }

//     // parameters {
//     //     string(name: 'WEB_SERVER_LIST', defaultValue: '116.118.95.121, 103.245.249.218', description: 'List of WEB Server use ","')
//     // }

//     stages {
//         stage('Generate Inventory File') {
//             steps {
//                 script {
//                     def webServers = params.WEB_SERVER_LIST.split(',').collect { it.trim() }
//                     echo "$webServers"
//                     // def inventoryContent = "[win]\n${webServers.join('\n')}\n\n[win:vars]\nansible_user=${ANSIBLE_CRED_USR}\nansible_password=${ANSIBLE_CRED_PSW}\nansible_port=5986\nansible_connection=winrm\nansible_winrm_server_cert_validation=ignore"
//                     // writeFile file: 'hosts.ini', text: inventoryContent
//                 }
//             }
//         }

//         // stage('Run update WEB') {
//         //     steps {
//         //         powershell(script: 'wsl ansible-playbook -i hosts.ini update_publish.yml')
//         //     }
//         // }
//     }

//     post {
//         always {
//             echo 'Finished'
//         }
//         success {
//             echo 'Succeeded!'
//         }
//         unstable {
//             echo 'Unstable :/'
//         }
//         failure {
//             echo 'Failed :('
//         }
//         changed {
//             echo 'Things were different before...'
//         }
//     }
// }


pipeline {
    agent any

    parameters {
        extendedChoice(
            name: 'PARENT_PARAMETER',
            type: 'PT_CHECKBOX',
            description: 'Select parent options',
            multiSelectDelimiter: ',',
            groovyScript: 'return ["Option1", "Option2", "Option3"]'
        )

        activeChoiceReactive(
            name: 'CHILD_PARAMETER',
            description: 'Select child options',
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    script: 'return ["Select a parent option first"]'
                ],
                script: [
                    classpath: [],
                    script: '''
                        def parentSelection = parentParameterValue ?: []
                        
                        if (parentSelection.contains("Option1")) {
                            return ["Option1.1", "Option1.2"]
                        } else if (parentSelection.contains("Option2")) {
                            return ["Option2.1", "Option2.2", "Option2.3"]
                        } else if (parentSelection.contains("Option3")) {
                            return ["Option3.1", "Option3.2", "Option3.3", "Option3.4"]
                        } else {
                            return ["Select a parent option first"]
                        }
                    '''
                ]
            ]
        )
    }

    stages {
        stage('User Input') {
            steps {
                script {
                    def parentSelection = params.PARENT_PARAMETER.split(',')
                    def childSelection = params.CHILD_PARAMETER.split(',')
                    
                    echo "Selected parent values: ${parentSelection}"
                    echo "Selected child values: ${childSelection}"

                    // Now you can use the selected values in your pipeline logic
                    // For example, run different steps based on the selected values
                }
            }
        }

        // Add more stages as needed
    }

    post {
        always {
            // Clean up or perform actions after the pipeline completes
        }
    }
}
