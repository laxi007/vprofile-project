pipeline {

agent any

    environment {
        registry = "laxi101/docker"
        registryCredential = 'docker101'
    }

    stages{
       stage('Fetch Code') {
            steps {
                git branch: 'cicd-kube', url: 'https://github.com/laxi007/vprofile-project.git'
            }
        }
      stage('BUILD'){
            steps {
                sh 'mvn clean install -DskipTests'
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
                sh 'mvn test -DskipTests'
            }
        }

        stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -DskipTests'
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

           steps{
                withSonarQubeEnv('sonar-pro'){
           
               sh ' mvn clean install -f webapp/pom.xml'
}
}
       

        

stage('Building image') {
            steps{
              script {
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
              }
            }
        }
        
        stage('Deploy Image') {
          steps{
            script {
              docker.withRegistry( '', registryCredential ) {
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
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
