pipeline {
    agent any

    stages {
        stage('Compile') {
            steps {
                gradlew('clean', 'classes')
            }
        }
        stage('Unit Tests') {
            steps {
                gradlew('test')
            }
            post {
                always {
                    junit '**/build/test-results/test/TEST-*.xml'
                }
            }
        }
        // stage('Long-running Verification') {
        //     environment {
        //         SONAR_LOGIN = credentials('Sonar-Login-Token')
        //     }
        //     parallel {
        //         stage('Integration Tests') {
        //             steps {
        //                 gradlew('integrationTest')
        //             }
        //             post {
        //                 always {
        //                     junit '**/build/test-results/integrationTest/TEST-*.xml'
        //                 }
        //             }
        //         }
        //         stage('Code Analysis') {
        //             steps {
        //                 gradlew('sonarqube')
        //             }
        //         }
        //     }
        // }
        stage('Assemble') {
            steps {
                gradlew('assemble')
            }
        }
        stage('Push to S3'){
          steps{
            withAWS(credentials:'aws-cli-creds'){
              s3Upload(file:'build/libs/todo-1.0.0.war', bucket:'swf-spring-boot', path:'todo-1.0.0.war')
            }
          }
        }

    }
}

def gradlew(String... args) {
    sh "./gradlew ${args.join(' ')} -s"
}
