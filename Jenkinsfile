pipeline {
agent any
    stages {
        stage('checkout') {
            steps {  checkout scm}
        }

      //  stage('check java') {
        //    steps {   sh "java -version"}
        //}

        stage('clean') {
            steps {
                sh "chmod +x mvnw"

            sh "./mvnw -ntp clean -P-webpack"
            }
        }
        stage('nohttp') {
            steps {
                sh "./mvnw -ntp checkstyle:check"

            }
        }

        stage('install tools') {
            steps {
                sh "./mvnw -ntp com.github.eirslett:frontend-maven-plugin:install-node-and-npm -DnodeVersion=v12.16.1 -DnpmVersion=6.14.5"

            }
        }

        stage('npm install') {
            steps {  sh "./mvnw -ntp com.github.eirslett:frontend-maven-plugin:npm"
            }
        }

        stage('backend tests') {
            steps {
            script {
                try {
                    sh "./mvnw -ntp verify -P-webpack"
                } catch (err) {
                    throw err
                } finally {
                    junit '**/target/test-results/**/TEST-*.xml'
                }
                }
            }
        }

        stage('frontend tests') {
            steps {
            script {
                try {
                    sh "./mvnw -ntp com.github.eirslett:frontend-maven-plugin:npm -Dfrontend.npm.arguments='run test'"
                } catch (err) {
                    throw err
                } finally {
                    junit '**/target/test-results/**/TEST-*.xml'
                }
                }
            }
        }

        stage('packaging') {
            steps {
                sh "./mvnw -ntp verify -P-webpack -Pprod -DskipTests"
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }
        stage('quality analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "./mvnw -ntp initialize sonar:sonar"
                }
            }
        }
    }
}