node {

  checkout scm

 environment {
      REGISTRY_URL = "https://hub.docker.com"
      REGISTRY_CREDENTIALS_ID = "docker_hub"
  }

  env.PATH = "${tool 'Maven3'}/bin:${env.PATH}"

  stage('Build') {
    sh 'mvn clean package -DskipTests'
  }
  stage('Test') {
    sh 'mvn -f pom.xml test'
  }
 
  stage('Deploy Docker Image') {
      docker.withRegistry('https://index.docker.io/v1', 'docker_hub' ){
          def cloudOps = docker.build("cloudops-app:${env.BUILD_NUMBER}")
          cloudOps.push()
          
          def cloudOpsLatest = docker.build("cloudops-app:latest")
          cloudOpsLatest.push()
      }
  }

  stage('Scan') {
    aquaMicroscanner imageName: "adriell/cloudops-app:${env.BUILD_NUMBER}", notCompliesCmd: 'exit 4', onDisallowed: 'fail', outputFormat: 'html'
  }


  stage ('Deploy Application') {
   // sh "docker run -name cloudops-app adriell/cloudops-app:${env.BUILD_NUMBER}"
   sh "echo Deploy"
  }
}
