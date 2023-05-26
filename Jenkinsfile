pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/manizt/CI-CD-Jenkins-Maven-SonarQube-Nexus.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
    
        }
        stage('Integration testing'){

            steps{
                sh 'mvn verify -DskipUnitTest'
            }
        }
        stage('Maven Build'){

            steps{
                sh 'mvn clean install'
            }

        }
        stage('Static code analysis'){

            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonarqube-secret') {
                        sh 'mvn clean package sonar:sonar'
                    }

                }
            }
        }
        stage('Quality Gate status') {
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-secret'
                }
            }
        }
        stage('upload war file to nexus'){
            steps{

                script{
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot',
                            classifier: '', file: 'target/Uber.jar',
                            type: 'jar'
                            ]
                              
                    ], 
                    credentialsId: 'nexus-auth',
                    groupId: 'com.example', 
                    nexusUrl: '34.229.148.251:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'demoapp-release', 
                    version: '1.0.0'
                }
            }
        }
    }
}