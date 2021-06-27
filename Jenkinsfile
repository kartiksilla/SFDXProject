#!groovy
import groovy.json.JsonSlurperClassic
def deploy(username, instanceUrl, keyfile, clientid) {
    script {
      sh """
         npm install sfdx-cli --global
         echo "y" | sfdx plugins:install sfdx-git-delta
         sfdx sgd:source:delta --to HEAD --from HEAD^ --output .
         echo 'DELTA TO DEPLOY:'
         cat ./package/*
         sfdx auth:jwt:grant --clientid ${clientid} \
         --jwtkeyfile ${keyfile} --username ${username} \
         --instanceurl ${instanceUrl}
         sfdx force:source:deploy -x ./package/package.xml -u ${username}
         """
  }
}

pipeline {
    agent { label 'aem-jenkins-agent' }

    tools {
        nodejs 'NodeJS'
    }

    stages {
        stage('Set parameters') {
            steps {
                script {
                    println 'KEY IS' 
                    println JWT_KEY_CRED_ID
                    println HUB_ORG
                    println SFDC_HOST
                    println CONNECTED_APP_CONSUMER_KEY
                    credentialsId = env.JWT_CRED_ID_DH
                    instanceUrl = env.HUB_ORG_DH
                    username = env.HUB_ORG_DH
                    clientid = env.CONNECTED_APP_CONSUMER_KEY_DH
                    if (env.BRANCH_NAME.equals("migration")) {
                        credentialsId = "salesforce-migration-key"
                        instanceUrl = "https://test.salesforce.com"
                        username = "shadab_hussain@epam.com.yamaha.migration"
                        clientid = "3MVG904d7VkkD2aNnhJt.id14IAX9JW2lwLI7YCWmXbK6Z8bwBhEb1W9Cahpkgtw0g5IfzXFhbEc837WtI0mU"
                    } else if (env.BRANCH_NAME.equals("qa-branch")) {
                        credentialsId = "salesforce-qa-key"
                        instanceUrl = "https://test.salesforce.com"
                        username = "shadab_hussain@epam.com.tst"
                        clientid = "3MVG9w8uXui2aB_qW5xdW0wp1KeiNA.V1Kb4xJ2kkuraHmv4ZriCMRZKSQ8Jc09cVUbVzT6557ZRNS9ykIF1P"
                    } else if (env.BRANCH_NAME.equals("master")) {
                        credentialsId = "salesforce-prod-key"
                        instanceUrl = "https://login.salesforce.com"
                        username = "shadab_hussain@epam.com.epamdev1"
                        clientid = "3MVG96mGXeuuwTZjj1Ln7Z0O5s.yEcBmxFrznlNmMI_noC_DKacLcEKFJTcN8HBwEtZbvmuJW68hbAzYzFpu7"
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    withCredentials([file(credentialsId: "${credentialsId}", variable: 'KEYFILE')]) {
                        deploy(username, instanceUrl, KEYFILE, clientid)
                    }
                }
            }
        }
	}
}
