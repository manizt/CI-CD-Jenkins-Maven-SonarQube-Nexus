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
    }
}