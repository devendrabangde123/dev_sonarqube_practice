def registry='https://trial4drys1.jfrog.io'
pipeline{
    agent any
    environment{
        PATH="/opt/maven/bin:$PATH"
    }
    stages{
        stage("build"){
            steps{
                echo "****building started****"
                sh "mvn clean deploy -Dmaven.test.skip=true"
                echo "****building completed****"
            }
        }
        stage("test"){
            steps{
                echo "****Unit testing started****"
                sh "mvn surefire-report:report"
                echo "****Unit testing completed****"
            }
        }
        stage("SonarQube analysis"){
            environment{
                scannerHome = tool 'saidemy-sonar-scanner'
            }
            steps{
                withSonarQubeEnv('saidemy-sonarqube-server'){
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage('Jar Publish'){
            steps{
                script{
                    echo "****Jar Publish started****"
                    def server = Artifactory.newServer url: registry + "artifactory", credentialsId: "artifact-cred"
                    
                    def properties = "buildid=${env.BUILD_ID}.commitid=${GIT_COMMIT}"
                    
                    def uploadSpec = """{
                    "files": [
                    {
                        "pattern": "jarstaging/(*)",
                        "target": "sai-libs-release-local/{1}",
                        "flat": "false",
                        "props": "${properties}"
                        "exclusion": ["*.sha1", "*.md5"]
                    }
                    ]
                }"""
                def buildInfo = server.upload(uploadspec)
                buildInfo.env.collect()
                server.publishBuildInfo(buildInfo)
                echo "****Jar Publish Ended****"
            }
        }
   }
} 
}
