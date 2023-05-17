pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/bienit/demo-counter-app.git'
                }
            }
        } 

         stage('UNIT Testing'){
            
            steps{
                sh 'mvn test'

            }
        }

        stage('Integration testing'){
            
            steps{
                sh 'mvn verify -DskipUnitTests'

            }
        }

        stage('Maven Build'){
            
            steps{
                sh 'mvn clean install'

            }
        }

        stage('SonarQube analysis'){
            
            steps{
                script{
                   withSonarQubeEnv(credentialsId: 'sonarqube-api-key') {
                     sh 'mvn clean package sonar:sonar'
                }
                }

            }
        } 

        stage('Quality Gate Status'){
            
            steps{
                script{
                   waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-api-key'
                }
                }

        }  

        stage('Upload war file to nexus'){
            
            steps{
                script{
                   def readPomVersion = readMavenPom file: 'pom.xml'
                   def nexusRepo = readMavenPom.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-realease"
                   nexusArtifactUploader artifacts: 
                   [
                     [
                          artifactId: 'springboot', 
                          classifier: '', 
                          file: 'target/Uber.jar', 
                          type: 'jar'
                     ]
                    ], 
                   credentialsId: 'nexus-auth', 
                   groupId: 'com.example', 
                   nexusUrl: '192.168.129.190:8081', 
                   nexusVersion: 'nexus3', 
                   protocol: 'http', 
                   repository: nexusRepo, 
                   version: "${readPomVersion.version}"
                }
            }

        }     
}
}