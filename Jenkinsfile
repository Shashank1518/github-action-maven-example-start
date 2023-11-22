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
	              sh 'sudo docker build '     
	              echo 'Build Image Completed'                
            }           
        }
        stage('Login to Docker Hub') {         
            steps{                            
    	          sh 'echo $dockerhubcredentials | sudo docker login -u $dockerhubcredentials --password-stdin'                 
    	          echo 'Login Completed'                
            }           
        }               
        stage('Push Image to Docker Hub') {         
            steps{                            
    	          sh 'sudo docker push dockerhubusername/dockerhubreponame:$BUILD_NUMBER'                
                  echo 'Push Image Completed'       
            }           
        } 
    	 stage('Logout to Docker Hub') {
       	     steps{  
          	    sh 'docker logout'           
         	  }      
      	    }
      }
}
