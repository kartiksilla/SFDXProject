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
    agent { label 'master' }

    tools {
        nodejs 'NodeJS'
    }

    stages {
        stage('Set parameters') {
            steps {
                script {
                    println 'KEY IS' 
                    credentialsId = env.JWT_CRED_ID_DH
                    instanceUrl = env.SFDC_HOST_DH
                    username = env.HUB_ORG_DH
                    clientid = env.CONNECTED_APP_CONSUMER_KEY_DH
                    
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
