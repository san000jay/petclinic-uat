/* This script is designed and maintained by Sanjay
*/
def notify(status) {
  mail (
        body:"""${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':
                 Check console output at,
                 href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]""",
        cc: '<valid-email-id>',
        subject: """JenkinsNotification: ${status}:""",
        to: 'san000jay@gmail.com'
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

def downloadSpec = """{
 "files": [
  {
      "pattern": "petclinic/*.war",
      "target": "./"
    }
 ]
}"""

pipeline {
 agent none
    stages {
      stage('SCM_Chekout') {
          agent { label "master" }
			steps {
			    script {
					notify('build-started')
				}
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/master']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    submoduleCfg: [],
                    userRemoteConfigs: [[url: 'https://github.com/san000jay/petclinic-uat.git']]])
            }
        }
      stage('Build'){
          agent { label "master" }
			steps {
                sh 'mvn -f pom.xml clean package'
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
          agent { label "node1" }
			steps {
			    script {
				  input('Deploy Package to Production?')
				  notify('Deployment-to-Production')
				}
					sh 'wget http://35.244.34.109:8081/artifactory/petclinic/petclinic.war'
					sh 'cp ./petclinic.war /opt/tomcat/webapps/'
					sh '/opt/tomcat/bin/catalina.sh run'

            }
         }
    }
}
