#!groovy

node {

    def SF_CONSUMER_KEY= '3MVG9fe4g9fhX0E5i71C9f6TEmSuTZSFfNiM66maHGNJPdruenYCulfJhxuRYGb_BymRYEPw_Nzz58xErp9.O'
    def SF_USERNAME= 'deploymentuser@targetorg.com'
    def SERVERKEY= env.SERVERKEY
    def DEPLOYDIR='src'
    def TEST_LEVEL='RunLocalTests'
    def SF_INSTANCE_URL = env.SF_INSTANCE_URL ?: "https://login.salesforce.com"


    def toolbelt = tool 'sfdx'

			println  toolbelt
    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        checkout scm
    }







        stage('Deploy Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${SERVERKEY} --setdefaultdevhubusername --instanceurl ${SF_INSTANCE_URL}"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile \"${SERVERKEY}\" --setdefaultdevhubusername --instanceurl ${SF_INSTANCE_URL}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d src/. -u ${SF_USERNAME}"
			}else{
			   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d src/. -u ${SF_USERNAME}"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        
    }
	
	stage('Logout') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:logout -u ${SF_USERNAME}-p"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:logout -u ${SF_USERNAME}-p"
            }
            if (rc != 0) { error 'LogOut Failed' }

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d src/. -u ${SF_USERNAME}"
			}else{
			   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d src/. -u ${SF_USERNAME}"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        
    }
	
	
}


