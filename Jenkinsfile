pipeline {
    agent any

    stages {

        stage('Notify Build Start') {
            steps {
                echo "Build Started for Sample WebApp 🚀"
            }
        }

        stage('Clone Code') {
            steps {
                git url: 'https://github.com/archauh/sample-webapp.git', branch: 'main'
            }
        }

        stage('Build + Test + Package') {
            steps {
                sh 'mvn clean verify'
            }
        }

        stage('Code Coverage (Jacoco)') {
            steps {
                sh 'mvn jacoco:report'
            }
            post {
                always {
                    jacoco execPattern: '**/target/jacoco.exec', classPattern: '**/target/classes', sourcePattern: '**/src/main/java'
                }
            }
        }

        stage('Dependency Check (OWASP)') {
            steps {
                sh 'mvn org.owasp:dependency-check-maven:check'
            }
        }

        stage('Static Analysis (SpotBugs)') {
            steps {
                sh 'mvn com.github.spotbugs:spotbugs-maven-plugin:spotbugs'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sshagent(['tomcat-ssh']) {
                    sh 'scp -o StrictHostKeyChecking=no target/sample-webapp.war ubuntu@13.202.162.73:/opt/tomcat/webapps/'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@13.202.162.73 "sudo systemctl restart tomcat"'
                }
            }
        }
    }

    post {
        always {
            junit '**/target/surefire-reports/*.xml'
            echo "Build Finished ✅"
        }
    }
}
