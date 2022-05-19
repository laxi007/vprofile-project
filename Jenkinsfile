
   
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
	 

       
    
      stage('BUILD'){
            steps {
                sh 'mvn clean install -DSkipTests '
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }

        stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -DSkipUnitTests'
            }
        }

        stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }
	    
 
   stage('CODE ANALYSIS with SONARQUBE') {

            environment {
                scannerHome = tool 'sonartool'
            }

            steps {
                withSonarQubeEnv('sonar-pro') {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
               
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
            sh "docker image prune -af"
          }
        }
	    
	

stage('Helm Deploy') {
	  agent { label 'kops' }
            steps {
                    
		 sh "helm upgrade --install --wait --timeout 180s vprofile-stack helm/vprofilecharts --set appimage=${registry}:${BUILD_NUMBER} --namespace default"
		 sh "kubectl get svc"

            
	    }
        }


    }



}

