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
      docker.withRegistry('https://registry.hub.docker.com', 'dockerHub' ){
        def  cloudops = docker.build("cloudops-app")
          cloudops.push("${env.BUILD_NUMBER}")
          cloudops.push("latest")
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
