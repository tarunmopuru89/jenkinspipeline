#!groovy

import groovy.json.JsonSlurperClassic

node {

    def SF_CONSUMER_KEY= '3MVG9fe4g9fhX0E5i71C9f6TEmSuTZSFfNiM66maHGNJPdruenYCulfJhxuRYGb_BymRYEPw_Nzz58xErp9.O'
    def SF_USERNAME= 'deploymentuser@targetorg.com'
    def SERVERKEY= env.SERVERKEY
    def DEPLOYDIR='src'
    def TEST_LEVEL='RunLocalTests'
    def SF_INSTANCE_URL = env.SF_INSTANCE_URL ?: "https://login.salesforce.com"


    def toolbelt = env.Toolbelt


    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        checkout scm
    }


    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------
    

             stage('Authorize DevHub') {
                rc = command  "\"${toolbelt}\" force:auth:jwt:grant --instanceurl ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${SERVERKEY} --setdefaultdevhubusername --setalias HubOrg"
                if (rc != 0) {
                    error 'Salesforce dev hub org authorization failed.'
                }
            }

			stage('Deploy Code') {
                rc = command  "\"${toolbelt}\" force:source:deploy --sourcepath force-app --targetusername ${SF_USERNAME} "
                if (rc != 0) {
                    error 'Salesforce Deployment  failed.'
                }
            }

			stage('Logout') {
                rc = command  "\"${toolbelt}\" force:auth:logout -u ${SF_USERNAME} -p"
                if (rc != 0) {
                    error 'Salesforce Logout  failed.'
                }
            }
}

def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
        return bat(returnStatus: true, script: script);
    }
}
