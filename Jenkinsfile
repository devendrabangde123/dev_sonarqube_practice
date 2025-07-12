pipeline{
    agent any
    environment{
        PATH="/opt/maven/bin:$PATH"
    }
    stages{
        stage("build"){
            steps{
                echo "****building started****"
                sh "mvn clean deploy - Dmaven.test.skip=true"
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
    }
}

