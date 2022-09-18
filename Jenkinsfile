def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE' : 'danger'
]
pipeline{
    agent any


    environment {
        NEXUSPASS = credentials('nexuspass')
    }

    stages{
       
        stage('Set up parameters'){
            steps{
                script{
                    properties([
                        parameters([
                            string(
                                defaultValue: '',
                                name: 'BUILDID',
                            ),
                            STRING(
                                defaultValue: '',
                                name: 'TIME'
                            )
                        ])
                    ])
                }
            }
        }
        stage('Ansible deploy to Prod'){
            steps{
                   ansiblePlaybook([
                    playbook: 'ansible/site.yml',
                    inventory: 'ansible/prod.inventory', 
                    credentialsId: 'applogin',
                    colorized: true,
                    installation: 'ansible',
                    disableHostKeyChecking: true,
                    extraVars: [
                            USER: 'admin',
                            PASS: "${NEXUSPASS}",
                            nexusip: '172.31.93.78',
                            reponame: 'vprofile-release',
                            groupid: 'QA',
                            time: "${env.TIME}",
                            build: "${env.BUILD}",
                            artifactid: 'vproapp',
                            vprofile_version: "vproapp-${env.BUILD}-${env.TIME}.war"
                    ]
                    
                    ])  
            }
        }
        
    }
    post {
        always{
            echo 'Slack Notification.'
            slackSend channel: '#jenkinscicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n"
        }
    }

}