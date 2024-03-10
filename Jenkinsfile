pipeline {
    agent any
    
    tools{
        maven 'maven3'
        jdk  'jdk17'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Ernestnyarko633/Deployment.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
               withSonarQubeEnv('sonar') {
                      sh    """ $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Full-stack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=sqp_3fcefc16b8c8af694fab9a22bf40392e4641c7b6 \
                      -Dsonar.java.binaries=."""
               }
            }
        }
        
        stage('OWASP Depedency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan./', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Deploy to Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'global-maven', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                  sh 'mvn deploy -DskipTest=true -X'
               }
            }
        }
    }
}
