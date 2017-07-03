stage 'Build' 
node {
  sh "sleep 10; echo 'Buld OK'"
}

stage 'Test'
parallel 'Integration': {
  node {
    sh "sleep 5; echo 'Integration Ok'"
  }
}, 'Quality': {
  node {
    sh "sleep 15; echo 'Quality Ok'"
  } 
}

stage name:'Performance', concurrency: 1
node {
  sh "sleep 7; echo 'Perform OK'"
}

stage 'Homologate'
parallel 'Windows': {
  node{
    sh "sleep 7; echo 'Windows OK'"
  }
}, 'Linux': {
  node{
    sh "sleep 10; echo 'Linux OK'"
  }
}, 'Mac': {
  node('slave-1'){
    sh "sleep 8; echo 'Mac OK'"
  }
}, 'Solaris': {
  node {
    sh "sleep 4; echo 'Solaris OK'"
  }
}

stage 'Load Config'
node('slave-1') {
  sh "sleep 9; echo 'Config Loaded.'"
}

stage 'Test Maven Docker'
parallel 'Maven-3.5-jdk-8':{
  docker.image('maven:3.5-jdk-8').inside('-v $HOME/.m2/repo:/m2repo') {
    git "https://github.com/ocanema/test-pipeline.git"
    sh 'echo $JAVA_HOME'
    sh 'mvn -Dmaven.repo.local=/m2repo validate' 
    sh 'echo "Maven 3.5 - JDK 8"'
  }
}, 'Maven-3.5-jdk-9':{
  docker.image('maven:3.5-jdk-9').inside('-v $HOME/.m2/repo:/m2repo') {
    git "https://github.com/ocanema/test-pipeline.git"
    sh 'echo $JAVA_HOME'
    sh 'mvn -Dmaven.repo.local=/m2repo validate' 
    sh 'echo "Maven 3.5 - JDK 9"'
  }
}

stage 'Test docker API'
node{
  sh "curl http://localhost:4243/version/"
}

stage 'Publish'
node('slave-1'){
  sh "echo 'Published...'"
}

stage 'Deploy'
parallel "Linux":{
  node('slave-1') {
    sh "sleep 9; echo 'Artifact Deployed!'"
  }
}, "Windows":{
  node('slave-1'){
    sh "sleep 9; echo 'Artifact Deployed!'"
  }
}
