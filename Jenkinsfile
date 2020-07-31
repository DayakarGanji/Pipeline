#!groovy


    environment {
        //getting the current stable/deployed revision...this is used in undeloy.sh in case of failure...
        stable_revision = sh(script: 'curl -H "Authorization: Basic $base64encoded" "https://api.enterprise.apigee.com/v1/organizations/dayakarg-eval/apis/HelloWorld-2/deployments" | jq -r ".environment[0].revision[0].name"', returnStdout: true).trim()
    }

    stages {
        stage('Initial-Checks') {
            steps {
                bat "npm -v"
                bat "mvn -v"
                echo "$apigeeUsername"
                echo "Stable Revision: ${env.stable_revision}"
        }}  
        stage('Policy-Code Analysis') {
            steps {
                bat "npm install -g apigeelint"
                bat "apigeelint -s HelloWorld-2/apiproxy/ -f codeframe.js"
            }
        }
                /*stage('Promotion') {
            steps {
                timeout(time: 2, unit: 'DAYS') {
                    input 'Do you want to Approve?'
                }
            }
        }*/
        stage('Deploy to Production') {
            steps {
                 //deploy using maven plugin
                 
                 // deploy only proxy and deploy both proxy and config based on edge.js update
                //	bat "sh && sh deploy.sh"
                bat "mvn -f HelloWorld-2/pom.xml install -Pprod -Dusername=${apigeeUsername} -Dpassword=${apigeePassword} -Dapigee.config.options=update"
            }
        }
        
    }

/*

using shared library for slack reporting
    the lib groovy script must be placed in a vars folder in SCM
using build job to call a Freestyle project which sends the Cucumber reports to slack
    currently the cucumberSlackSend channel: 'apigee-cicd', json: '$WORKSPACE/reports.json' 
        option doesnt send the reports to Slack
*/
