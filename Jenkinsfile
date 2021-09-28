#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=
    def RUN_ARTIFACT_DIR
    def SFDC_USERNAME

    def HUB_ORG=='microservice.dev1@salesforce.com.dev1'
    def SFDC_HOST = 'https://test.salesforce.com'
    def JWT_KEY_CRED_ID ='ccdb81a5-9be7-4ce0-a10d-656447a1b1de'
    def CONNECTED_APP_CONSUMER_KEY='3MVG9TYG6AjyhS_J0ZemfYlFhBWpTUulOFxoAqJTAp0B0E0Y1JXvDAIAXg_SlEKyNH7rS5DvBM0K40BjLEikZ'
      
    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploye Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}else{
			   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}
