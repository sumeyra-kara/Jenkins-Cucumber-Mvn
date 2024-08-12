pipeline {
    agent any
    tools {
        maven 'MAVEN'
        jdk 'JDK'
    }
    options {
        timestamps ()
        ansiColor('gnome-terminal')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    triggers {
        cron('0 6 * * 1-5')
    }
    
    parameters {
        string(name: 'TagName', defaultValue: "@employee", description: 'Scenario Tag to be run')
    }
    
    stages {
        stage('Initialize') {
            steps {
                bat '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
            }
        }
        stage('Build') {
            steps {
                bat "mvn -f pom.xml -B -DskipTests clean package"
            }
            post {
                success {
//                     echo "Now Archiving the Artifacts....."
                    archiveArtifacts artifacts: '**/*.jar'
                }
            }
        }
        stage('Test') {
            steps {
                bat "mvn -f pom.xml test"
                bat "mvn clean verify -Dcucumber.filter.tags='$params.TagName' -DfailIfNoTests=false"
            }
            post {
                always {
                    cucumber 'target/cucumber-reports/cucumber.json'
                }
            }

        }
//         stage('Cucumber Report') {
//             steps {
//                 cucumber buildStatus: "UNSTABLE",
//                     fileIncludePattern: "**/cucumber.json",
//                     jsonReportDirectory: "target"
//             }
//         }
    }
}

