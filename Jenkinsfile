pipeline {
	agent any
	options {
		buildDiscarder(logRotator(daysToKeepStr: '3', numToKeepStr: '5'))
	}

	stages {
   		stage('Preparation') { // for display purposes
			steps {
    				git branch: 'test-promotion', url: 'https://github.com/roguetech/simple-java-maven-app.git'
   			}
		}
   		stage('Build') {
			steps {
    				sh "mvn clean compile test"
   			}
		}
   		stage('Results') {
			steps {
				echo "Results"
			}    
   		}
   		stage('Push') {
			steps {
				script {
					def server = Artifactory.newServer url: 'http://192.168.169.130:8081/artifactory', username: 'admin', password: 'password!'
        				def uploadSpec = """{
            					"files": [
                					{
                						"pattern": "*",
                						"target": "libs-release-local/com/vsware/services/test-app1/${BUILD_NUMBER}/"
                					}
            					]
            				}"""
        				def buildInfo = Artifactory.newBuildInfo()
					buildInfo.retention maxBuilds: 5, maxDays: 7, deleteBuildArtifacts: true
        				server.upload spec: uploadSpec, buildInfo: buildInfo
        				server.publishBuildInfo buildInfo
   				}
			}
		}
   		stage('Promotion') {
				when { tag "prod" }
					steps {
						echo "promoting this"
					}
		}
	}
}
