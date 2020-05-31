node {

  checkout scm

  def cloudops
  env.PATH = "${tool 'Maven3'}/bin:${env.PATH}"

  stage('Build') {
    sh 'mvn clean package -DskipTests'
  }
  stage('Run Test') {
    sh 'mvn -f pom.xml test'
  }

  stage('Sonar Report') {
      sh 'mvn verify sonar:sonar'
  }
 
  stage('Deploy Docker Image') {
      try {
        docker.withRegistry('', 'dockerHubCredential' ){
            cloudops = docker.build("adriell/cloudops-app")
            cloudops.push("${env.BUILD_NUMBER}")
            cloudops.push("latest")
        }
      }catch (error) {       
      } finally {
          sh "docker rm -f adriell/cloudops-app:${env.BUILD_NUMBER}"
          sh "docker rm -f adriell/cloudops-app:latest"
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
