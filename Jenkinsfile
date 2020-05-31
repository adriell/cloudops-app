node {
  checkout scm
  env.PATH = "${tool 'Maven3'}/bin:${env.PATH}"
  stage('Build') {
    sh 'mvn clean package -DskipTests'
  }
  stage('Test') {
    sh 'mvn -f pom.xml test'
  }
  stage('Create Docker Image') {
    dir('webapp') {
      docker.build("adriell/cloudops-app:${env.BUILD_NUMBER}")
      docker.build("adriell/cloudops-app:latest")
    }
  }

  stage ('Run Application') {
    try {
      sh "docker run adriell/cloudops-app:${env.BUILD_NUMBER} -name cloudopps-app"
    } catch (error) {
    } finally {
      sh 'docker rm -f cloudopps-app'
    }
  }

}
