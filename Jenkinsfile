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


            // -------------------------------------------------------------------------
            // Create new scratch org to test your code.
            // -------------------------------------------------------------------------

            stage('Create Test Scratch Org') {
                rc = command "${toolbelt} force:org:create --targetdevhubusername HubOrg --setdefaultusername --definitionfile config/project-scratch-def.json --setalias ciorg --wait 10 --durationdays 1"
                if (rc != 0) {
                    error 'Salesforce test scratch org creation failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Display test scratch org info.
            // -------------------------------------------------------------------------

            stage('Display Test Scratch Org') {
                rc = command "${toolbelt} force:org:display --targetusername ciorg"
                if (rc != 0) {
                    error 'Salesforce test scratch org display failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Push source to test scratch org.
            // -------------------------------------------------------------------------

            stage('Push To Test Scratch Org') {
                rc = command "${toolbelt} force:source:push --targetusername ciorg"
                if (rc != 0) {
                    error 'Salesforce push to test scratch org failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Run unit tests in test scratch org.
            // -------------------------------------------------------------------------

            stage('Run Tests In Test Scratch Org') {
                rc = command "${toolbelt} force:apex:test:run --targetusername ciorg --wait 10 --resultformat tap --codecoverage --testlevel ${TEST_LEVEL}"
                if (rc != 0) {
                    error 'Salesforce unit test run in test scratch org failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Delete test scratch org.
            // -------------------------------------------------------------------------

            stage('Delete Test Scratch Org') {
                rc = command "${toolbelt} force:org:delete --targetusername ciorg --noprompt"
                if (rc != 0) {
                    error 'Salesforce test scratch org deletion failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Create package version.
            // -------------------------------------------------------------------------

            stage('Create Package Version') {
                if (isUnix()) {
                    output = sh returnStdout: true, script: "${toolbelt} force:package:version:create --package ${PACKAGE_NAME} --installationkeybypass --wait 10 --json --targetdevhubusername HubOrg"
                } else {
                    output = bat(returnStdout: true, script: "${toolbelt} force:package:version:create --package ${PACKAGE_NAME} --installationkeybypass --wait 10 --json --targetdevhubusername HubOrg").trim()
                    output = output.readLines().drop(1).join(" ")
                }

                // Wait 5 minutes for package replication.
                sleep 300

                def jsonSlurper = new JsonSlurperClassic()
                def response = jsonSlurper.parseText(output)

                PACKAGE_VERSION = response.result.SubscriberPackageVersionId

                response = null

                echo ${PACKAGE_VERSION}
            }


            // -------------------------------------------------------------------------
            // Create new scratch org to install package to.
            // -------------------------------------------------------------------------

            stage('Create Package Install Scratch Org') {
                rc = command "${toolbelt} force:org:create --targetdevhubusername HubOrg --setdefaultusername --definitionfile config/project-scratch-def.json --setalias installorg --wait 10 --durationdays 1"
                if (rc != 0) {
                    error 'Salesforce package install scratch org creation failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Display install scratch org info.
            // -------------------------------------------------------------------------

            stage('Display Install Scratch Org') {
                rc = command "${toolbelt} force:org:display --targetusername installorg"
                if (rc != 0) {
                    error 'Salesforce install scratch org display failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Install package in scratch org.
            // -------------------------------------------------------------------------

            stage('Install Package In Scratch Org') {
                rc = command "${toolbelt} force:package:install --package ${PACKAGE_VERSION} --targetusername installorg --wait 10"
                if (rc != 0) {
                    error 'Salesforce package install failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Run unit tests in package install scratch org.
            // -------------------------------------------------------------------------

            stage('Run Tests In Package Install Scratch Org') {
                rc = command "${toolbelt} force:apex:test:run --targetusername installorg --resultformat tap --codecoverage --testlevel ${TEST_LEVEL} --wait 10"
                if (rc != 0) {
                    error 'Salesforce unit test run in pacakge install scratch org failed.'
                }
            }


            // -------------------------------------------------------------------------
            // Delete package install scratch org.
            // -------------------------------------------------------------------------

            stage('Delete Package Install Scratch Org') {
                rc = command "${toolbelt} force:org:delete --targetusername installorg --noprompt"
                if (rc != 0) {
                    error 'Salesforce package install scratch org deletion failed.'
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
