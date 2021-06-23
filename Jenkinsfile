#!groovy

node {

    def SF_CONSUMER_KEY= env.SF_TARGET_ORG_CONSUMER_KEY
    def SF_USERNAME= env.SF_Target_Org_Username
    def SERVER_KEY_CREDENTIALS_ID= env.SERVERKEY
    def DEPLOYDIR='src'
    def TEST_LEVEL='RunLocalTests'
    def SF_INSTANCE_URL = env.SF_INSTANCE_URL ?: "https://login.salesforce.com"


    def toolbelt = tool 'Toolbelt'

			println  toolbelt
    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        checkout scm
    }
	

        stage('Deploye Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${SERVER_KEY_CREDENTIALS_ID} --setdefaultdevhubusername --instanceurl ${SF_INSTANCE_URL}"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile \"${SERVER_KEY_CREDENTIALS_ID}\" --setdefaultdevhubusername --instanceurl ${SF_INSTANCE_URL}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${SF_USERNAME}"
			}else{
			   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d manifest/. -u ${SF_USERNAME}"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    
}


