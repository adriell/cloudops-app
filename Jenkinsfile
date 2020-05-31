node {

  checkout scm

  environment {
      registry = "adriell/cloudops-app"
      registryCredential = "docker_hub"
  }

  env.PATH = "${tool 'Maven3'}/bin:${env.PATH}"

  stage('Build') {
    sh 'mvn clean package -DskipTests'
  }
  stage('Test') {
    sh 'mvn -f pom.xml test'
  }
 
  stage('Deploy Docker Image') {
      steps{
          script{
               docker.build registry + ":${env.BUILD_NUMBER}"
               docker.build registry + ":latest"
          }
      }
  }

  stage('Scan') {
    aquaMicroscanner imageName: "adriell/cloudops-app:${env.BUILD_NUMBER}", notCompliesCmd: 'exit 4', onDisallowed: 'fail', outputFormat: 'html'
  }


  stage ('Deploy Application') {
   // sh "docker run -name cloudops-app adriell/cloudops-app:${env.BUILD_NUMBER}"
   sh "echo Deploy"
  }

//stage ('LoadTeste') {
//  sh 'loadteste'
//}

}
