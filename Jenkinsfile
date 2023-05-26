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
                    withSonarQubeEnv(credentialsId: 'sonarqube') {
                        sh 'mvn clean package sonar:sonar'
                    }

                }
            }
        }
        stage('Quality Gate status') {
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube'
                }
            }
        }
        stage('upload war file to nexus'){
            steps{

                script{

                    def readPomVersion = readMavenPom file: 'pom.xml'
                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot', 
                            classifier: '', 
                            file: 'target/Uber.jar', 
                            type: 'jar'
                            ]
                    ], 
                    credentialsId: 'nexus', 
                    groupId: 'com.example', 
                    nexusUrl: '54.174.217.139:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: nexusRepo, 
                    version: "${readPomVersion.version}"

                }
            }
        }
        stage('Docker Image Build'){
            steps{
                script{
                    def repositoryName = "demoapplication" // Change this to your desired lowercase repository name
                    def tag = "v1.${BUILD_ID}"
            
                    sh "docker image build -t ${repositoryName}:${tag} ."
                    sh "docker image tag ${repositoryName}:${tag} manizt/${repositoryName}:${tag}"
                    sh "docker image tag ${repositoryName}:${tag} manizt/${repositoryName}:latest"
                }
            }
        }
    }
}