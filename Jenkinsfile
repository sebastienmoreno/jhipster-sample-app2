node {
  stage('Checkout project') { 
    checkout scm
  }

  docker.image('jhipster/jhipster').inside {
    stage('build app'){
      sh './mvnw package -Pprod'
      step([$class: 'JUnitResultArchiver', allowEmptyResults: true, healthScaleFactor: 20, testResults: '**/target/surefire-reports/*.xml'])
    }
  }

  if (env.BRANCH_NAME ==~ 'master|develop') {
    stage('build docker image'){
        sh 'docker build -t jhipstersampleapplication .'
    }

    stage('push image to dockerhub'){
      withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: "dockerhub", usernameVariable: 'DOCKER_HUB_USER', passwordVariable: 'DOCKER_HUB_PASSWORD']]) {
        sh 'docker login -u $DOCKER_HUB_USER -p $DOCKER_HUB_PASSWORD'
        sh 'docker tag jhipstersampleapplication $DOCKER_HUB_USER/jhipster-sample-app:latest'
        sh 'docker push $DOCKER_HUB_USER/jhipster-sample-app:latest'
      }
    }
    
    docker.image('maven:3.6.0-jdk-8-alpine').inside {
      stage('SonarQube analysis') {
        withSonarQubeEnv('sonarqube') {
          sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
        }
      }
    }
  }
}
