node {
/*stage('Checkout project') { 
  git 'https://github.com/sebastienmoreno/jhipster-sample-app.git'
}*/
stage('Checkout project') { 
  checkout scm
}
stage('Build App') {
  sh 'docker run --rm -t -u $(id -u) -v $(pwd):$(pwd) -w $(pwd) --volumes-from jenkins-data jhipster/jhipster ./mvnw package -Pprod'
}
  stage('build docker image'){
  sh 'docker run --rm -t -u $(id -u) -v $(pwd):$(pwd) -w $(pwd) --volumes-from jenkins-data jhipster/jhipster ./mvnw docker:build'
}
stage('push image to dockerhub'){
 withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: "dockerhub", usernameVariable: 'DOCKER_HUB_USER', passwordVariable: 'DOCKER_HUB_PASSWORD']]) {
    sh 'docker login -u $DOCKER_HUB_USER -p $DOCKER_HUB_PASSWORD'
  }
  sh 'docker tag jhipstersampleapplication $DOCKER_HUB_USER/jhipster-sample-app:latest'
  sh 'docker push $DOCKER_HUB_USER/jhipster-sample-app:latest'
 }
}
