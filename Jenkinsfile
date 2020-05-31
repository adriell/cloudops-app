node {
  checkout scm
  env.PATH = "${tool 'Maven3'}/bin:${env.PATH}"
  stage('Update Ansible') {
    sh 'mvn clean package -DskipTests'
  }
  stage('Build') {
    sh 'mvn clean package -DskipTests'
  }
  stage('Test') {
    sh 'mvn -f pom.xml test'
  }
 // stage('Sonar') {
 //   sh 'mvn -f pom.xml test'
 // }
  stage('Create Docker Image') {
    docker.build("adriell/cloudops-app:${env.BUILD_NUMBER}")
    docker.build("adriell/cloudops-app:latest")
  }

  stage('Scan') {
        steps{
            aquaMicroscanner imageName: "adriell/cloudops-app:${env.BUILD_NUMBER}", notCompliesCmd: 'exit 4', onDisallowed: 'fail', outputFormat: 'html'
         }
    }


  stage ('Deploy Application') {
    sh "docker run -name cloudops-app adriell/cloudops-app:${env.BUILD_NUMBER}"
  }

//stage ('LoadTeste') {
//  sh 'loadteste'
//}

}
