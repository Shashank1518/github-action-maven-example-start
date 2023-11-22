pipeline {
    agent any
    environment {     
      DOCKERHUB_CREDENTIALS= credentials('dockerhubcredentials')     
    } 
    stages {
        stage('Build') {
            steps {
                sh 'make'
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            }
        }
        stage('Test') {
            steps {
                /* `make check` returns non-zero on test failures,
                * using `true` to allow the Pipeline to continue nonetheless
                */
                sh 'make check || true' 
                junit '**/target/*.xml' 
            }
        }
        stage('Deploy') {
            when {
              expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
              }
            }
            steps {
                sh 'make publish'
            }
        }
        stage('Build Docker Image') {  
            steps{                     
	              sh 'sudo docker build -t <dockerhubusername>/<dockerhubreponame>:$BUILD_NUMBER .'     
	              echo 'Build Image Completed'                
            }           
        }
        stage('Login to Docker Hub') {         
            steps{                            
    	          sh 'echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'                 
    	          echo 'Login Completed'                
            }           
        }               
        stage('Push Image to Docker Hub') {         
            steps{                            
    	          sh 'sudo docker push dockerhubusername/dockerhubreponame:$BUILD_NUMBER'                
                echo 'Push Image Completed'       
            }           
        }      
    post{
       always {  
           sh 'docker logout'           
           }      
        }
    }
}
