/* This script is designed and maintained by Ganesh Palnitkar
*/
def notify(status) {
  mail (
        body:"""${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':
                 Check console output at,
                 href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]""",
        cc: '<valid-email-id>',
        subject: """JenkinsNotification: ${status}:""",
        to: '<alid-email-id>'
       )
 }
def server = Artifactory.server 'artifactory'
def uploadSpec = """{
  "files": [
	{
	  "pattern": "target/petclinic.war",
	  "target": "petclinic/"
	}
		   ]
  }"""

/*def downloadSpec = """{
 "files": [
  {
      "pattern": "petclinic/*.war",
      "target": "./"
    }
 ]
}"""
*/
pipeline {
 agent none
    stages {
      stage('SCM_Chekout') {
          agent { label "master" }
			steps {
//			    script {
//					notify('build-started')
//				}
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/master']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    submoduleCfg: [],
                    userRemoteConfigs: [[url: 'https://github.com/ganeshhp/PetClinicProject-UAT.git']]])
            }
        }
      stage('Build'){
          agent { label "master" }
		steps {
                    sh 'mvn clean package'
            }
        }

	  stage('push-to-artifactory') {
          agent { label "master" }
		steps {
                 script {
     		    server.upload(uploadSpec)
            }
        }
	}
	  stage('Deploy') {
          agent { label "appserver" }
			steps {
//			    script {
//				  input('Deploy Package to Production?')
//				  notify('Deployment-to-Production')
//				}
					sh 'curl -u admin:password123 -O "http://35.231.176.62:8081/artifactory/petclinic/petclinic.war"'
					sh 'cp ./petclinic.war /opt/tomcat/webapps/'
					sh '/opt/tomcat/bin/catalina.sh run'

            }
         }
    }
}

