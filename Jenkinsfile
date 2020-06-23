#!groovy
import groovy.json.JsonSlurperClassic
println 'Vaibhav'
node {
println 'Shukla' 
    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'
	println toolbelt
    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
	rc = bat returnStatus: true, script: "file\\C:\Program Files\Salesforce CLI\bin\sfdx force:auth:jwt:grant --clientid 3MVG97quAmFZJfVxVPVydBctzw3eNMGEfEqfNfXPGe1.J06P6_4ZID18aC5NIDjhZEyM2HHYuBAL.DP0fLt9p --jwtkeyfile server.key --username sourceorg@jenkins.com --instanceurl https://login.salesforce.com"
        stage('Deploye Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "file\\\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
                 //rc = bat returnStatus: true, script: sfdx force:auth:jwt:grant --clientid 3MVG97quAmFZJfVxVPVydBctzw3eNMGEfEqfNfXPGe1.J06P6_4ZID18aC5NIDjhZEyM2HHYuBAL.DP0fLt9p --jwtkeyfile server.key --username sourceorg@jenkins.com --instanceurl https://login.salesforce.com
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
