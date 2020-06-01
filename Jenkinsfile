node {

  checkout scm

  def cloudops
  env.PATH = "${tool 'Maven3'}/bin:${env.PATH}"
  stage('Update Ansible') {
      sh 'cd /opt/cloudops/ && git reset --hard HEAD && git pull'
  }
  
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
      } catch (error) {       
      } finally {
          sh "docker rmi -f adriell/cloudops-app:${env.BUILD_NUMBER}"
          sh "docker rmi -f adriell/cloudops-app:latest"
      }
  }
  stage('Scan') {
    aquaMicroscanner imageName: "adriell/cloudops-app:${env.BUILD_NUMBER}", notCompliesCmd: 'exit 4', onDisallowed: 'fail', outputFormat: 'html'
  }


  stage ('Deploy Application') {
   sh "cd /opt/cloudops/ansible && /usr/local/bin/ansible-playbook -i inventory local.yml --extra-vars 'container_name=cloudops docker_image=adriell/cloudops-app:${env.BUILD_NUMBER}'"
  }
  
  stage ('Notify') {
   sh "echo OK"
  }
}
