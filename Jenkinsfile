
def buildnumber = Jenkins.instance.getItem('cicd-jenkins-bean-stage').lastSuccessfulBuild.number

def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE' : 'danger'
]
pipeline{
    agent any
    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }

    environment {
        ARTIFACT_NAME = "vprofile-v${buildnumber}.WAR"
        AWS_S3_BUCKET = 'chakrivprocicdbean'
        AWS_EB_APP_NAME = 'vproapp1'
        AWS_EB_APP_VERSION = "${buildnumber}"
        AWS_EB_ENVIRONMENT= 'Vproapp1-prod-env'
    }

    stages{

        stage('Deploy to BeanStalk Staging'){
            steps{
                withAWS(credentials: 'awsbeancreds' , region: 'us-east-1'){
                    sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
                }
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