pipeline {
    
    agent any
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                script{
                   git branch: 'main', url: 'https://github.com/prajwalbenjamin/Demo_app.git'
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
                
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
         stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar-api') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                   }
                    
                }
           }
        stage('Quality Gate status'){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api'
                }   
            }
         }
        stage('Upload war file to nexus'){
            steps{
                script{
                    
                    def readPomVersion = readMavenPom file: 'pom.xml'
                    
                    def nexusrepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
                    
                    nexusArtifactUploader artifacts: [
                        [
                            artifactId: 'springboot', 
                            classifier: '', 
                            file: 'target/Uber.jar', 
                            type: 'jar'
                        ]
                    ], 
                        credentialsId: 'nexus-auth', 
                        groupId: 'com.example', 
                        nexusUrl: 'localhost:8081/', 
                        nexusVersion: 'nexus3', 
                        protocol: 'http', 
                        repository: nexusrepo, 
                        version: "${readPomVersion.version}"
                }
            }
        }
        stage('Docker Image Build'){
            steps{
                script{
                    sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID prajwalbenjamin/$JOB_NAME:v1.$BUILD_ID'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID prajwalbenjamin/$JOB_NAME:latest'
                }
            }
        }
        stage('Push image to DockerHub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'dockerhub-cred')]){
                        sh 'docker login -u prajwalbenjamin -p Prajben@0527'
                        sh 'docker image push prajwalbenjamin/$JOB_NAME:v1.$BUILD_ID'
                        sh 'docker image push prajwalbenjamin/$JOB_NAME:latest'
                   }
                }
            }
        }
    }    
}
