pipeline {

agent any
   

    stages{

       stage('Source Composition Analysis'){
        steps{
        sh 'rm owasp* || true'
        sh 'wget  "https://raw.githubusercontent.com/laxi007/vprofile-project/master/owasp-dependency-check.sh"'
        sh 'chmod +x owasp-dependency-check.sh'
        sh 'bash owasp-dependency-check.sh'
}
}
}
}
