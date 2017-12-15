pipeline {
    agent any

    environment {
      SONAR_LOGIN = credentials('Sonar-Login-Token')
    }

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
        stage('Code Analysis') {
            steps {
                gradlew('sonarqube')
            }
        }
        stage('Assemble') {
            steps {
                gradlew('assemble')
            }
        }
        // stage('Push to S3'){
        //   steps{
        //     withAWS(credentials:'aws-cli-creds'){
        //       s3Upload(file:'build/libs/todo-1.0.0.war', bucket:'swf-spring-boot', path:'todo-1.0.0.war')
        //     }
        //   }
        // }

        stage('Push to nexus'){
          steps{
            gradlew('publish')
          }
        }

        stage('Run AWX playbook'){
          steps{
            milestone(1)
            input('Deploy via AWX?')
            ansibleTower(
              towerServer: 'Dev Tower',
              jobTemplate: 'Deploy Spring Boot'
            )
          }
        }

    }
}

def gradlew(String... args) {
    sh "./gradlew ${args.join(' ')} -s"
}
