#!groovy

node {

    def SF_CONSUMER_KEY= '3MVG9fe4g9fhX0E7z0MhUI9pnzxUTVnerl91vHNCKg6jODAmKE8cbQOFnv3qukcYb.bbW0EWkgW4y2jSibeZE'
    def SF_USERNAME= 'deploymentuser@qa2org.com'
    def SERVER_KEY_CREDENTIALS_ID= '-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAnraQmm2gV9iLu8wYI1olAmo0qAgRGvPO7WXqLEhK6h+a2hu4
ge56PJI5apbq2qBBEIfKqmp33WsutBfheO55xSueq/NhyhbfyBtYc6WbXJrZG+J2
1Sw+LA3sIG6YU9/y5CvfrgjdWHvrH0BwQ9phf6/uSS0ptz0410dvvMKRcRgb+kbO
r/6QtLGdzBcL6rd1thAeHPVKSQrLiRAoCHxOm22KX6saX04BRsU4ts68kqzktOn8
gs69/YkL/MOi13Yab/Yim14EGvrq2EJ0jxtXNtWXgSOaVxqv0MHuKkVn1K6PKcoQ
+jOdP2CI41mG/UjRCF1Ad7tCGUAorIaqZYtQAQIDAQABAoIBAETQRSwfwfi0lAlN
uV2ClS9R2xjLvpbgBOUbXgfrJEgUgfB1Om9jda5Te/+CBbva5bsEFVQEK4peEXbW
L9VeGH/rpLVLJigX+NjrOlOSByWEogOcEgflUeOJ+coqCXO8UrSpbScpAsd9mvkG
2GYjBkj1f6xMn5yqN73nZEQEXEkbCu+zGN1lF9a1LkxE/UY7JEtDgxBhPCuw79/6
P/DjZiOZVqQvXWpwFbWyCGsPaUvsDPlpzzIfPn8DTg31T+BD+bIf33I37OMOs/Ei
L17+3JuZoqZqKnHpyVh7tJ0TyvV5xVIdzCWVVjVw/r3nviTopOj+n0WlNT9XUkFR
FPc4ub0CgYEA032A8KVnW2eSs3cSw5xk+kW8wmHFLsP/svhgjTn/q63v/H9E77l7
01qrRN17madS/yyPjVafg2IWsJiHem+rmp1yvMoedwsU+Yrxj7Gu12CEZq1DnikL
jdtwncXPV+i0YL0KQvu9h+TYEgl0Ohke0OyFqiCgjPMy1NehcfOVtQcCgYEAwB2U
gArqVcy4PGvH3GrS7rNPy/PMwbckqxf4CAsd1w5AyL0gpS0lRWS/YXk7mc/6SmNK
dIrzGqhnZqs5pU43AgrOs5gUqmol+MybjLvs0FKEfNIjRulcZPCvtn9AUjytHg5N
rBIIv4PI8T5mPct2X8XlusDn0Jz2BiNx1eOv2LcCgYAAs0SvB00tT018DKPiQ+1N
qtdyKVK20e1R0WK4dP20utG1m1JGHO4dCArTIoybOKOctrAO/r9udu+uTAL+08nU
rDrKBz1MlZvPK7ebCzKSAf7OPDqiFm60XIql6xbBqsKWI2oaSK0a+xYAEUnyO00P
0girRIpjjRaY/9HIB89yFwKBgQC8ML21J/whDCc1WMcxDZuOpCv4t9vNrt/GkfYv
uuQCND4V5d5Cr5ShA23Nx/owU+D0WYsn2q0FYg3YLsaLPbD42SleGA22WcDUlWmS
VMxHzW1m8FoKLrKJVpRpiwGxDSrwFEbV1dyn7io610tEITjV3H+Lj0gFvJvrq3Dp
et5YxQKBgEi94qlc7xRFZcvE3+7MSTsYvpL2a1QGQOjVK3b+xb9vI31YyXLOaYo1
opunCi9HqJC5C77x21U1DurEeefzv9AxEXZBps4TOEzsOHgt6JawOpUPXcuXHzma
63PsaQYmxbM3P8I2WkaBjPLNjdwROPbY/o4QftzeiSk1zJZtC1mP
-----END RSA PRIVATE KEY-----'
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


