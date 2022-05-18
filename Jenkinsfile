pipeline {

agent any

    environment {
       
        registry = "ec2-3-80-31-183.compute-1.amazonaws.com:8085/docker-reg"
        registryCredential = "nexusserverlogin" 
       
    }

    stages{
       stage('Fetch Code') {
            steps {
                git branch: 'cicd-kube', url: 'https://github.com/laxi007/vprofile-project.git'
            }
        }
	 

       
    
      

stage('Building image') {
            steps{
              script {
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
              }
            }
        }
        
        stage('Push Image') {
          steps{
            script {
              docker.withRegistry( 'http://'+registry, registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                
              }
            }
          }
        }

        stage('Remove Unused docker image') {
          steps{
            sh "docker rmi $registry:$BUILD_NUMBER"
          }
        }

stage('Kubernetes Deploy') {
	  agent { label 'kops' }
            steps {
                    sh "helm upgrade --install --force vprofile-stack helm/vprofilecharts --set appimage=${registry}:${BUILD_NUMBER} --namespace prod"
            }
        }


    }



}
