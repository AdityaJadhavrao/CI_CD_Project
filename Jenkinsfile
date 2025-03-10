def COLOR_MAP = [
    'SUCCESS': '#00FF00',
    'FAILURE': '#FF0000',
    'ABORTED': '#FFFF00',
    'NOT_BUILT': '#808080',
    'UNSTABLE': '#FFA500'
]

pipeline {
    agent any

    tools {
        maven "MAVEN3"
        jdk "OracleJDK17"
    }

    environment {
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = 'Aditya@1139*'
        RELEASE_REPO = 'vprofile-release'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUSIP = '98.82.168.50'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_LOGIN = 'nexuslogin'
        SONARSERVER = 'sonarserver'
        SONARSCANNER = 'sonarscanner'
        registryCredential = 'ecr:ap-southeast-2:awscreds'
        appRegistry = '490135005964.dkr.ecr.ap-southeast-2.amazonaws.com/vprofileappimg'
        vprofileRegistry = "https://490135005964.dkr.ecr.ap-southeast-2.amazonaws.com"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }
            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Checkstyle Analysis') {
            steps {
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }
	stage('CODE ANALYSIS with SONARQUBE') {
           environment {
               scannerHome = tool "${SONARSCANNER}"
           }
           steps {
               withEnv(["JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64", "PATH+JAVA=${JAVA_HOME}/bin"]) {
                   withSonarQubeEnv("${SONARSERVER}") {
                       sh '''${scannerHome}/bin/sonar-scanner \
                       -Dsonar.projectKey=vprofile \
                       -Dsonar.projectName=vprofile-repo \
                       -Dsonar.projectVersion=1.0 \
                       -Dsonar.sources=src/ \
                       -Dsonar.java.binaries=target \
                       -Dsonar.junit.reportsPath=target/surefire-reports/ \
                       -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                       -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
        }
     }
  }
}

