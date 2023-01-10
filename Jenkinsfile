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
    }    
}
