node ('docker-slave') {
  withCredentials([file(credentialsId: 'K8S_CA', variable: 'K8S_CA'), file(credentialsId: 'K8S_CERT', variable: 'K8S_CERT'), file(credentialsId: 'K8S_CONFIG', variable: 'K8S_CONFIG'), file(credentialsId: 'K8S_KEY', variable: 'K8S_KEY')]) {
    docker.withRegistry('https://gitlab-registry.cern.ch', 'ba4fc2f2-51c5-4a97-b1b2-b80ee5b2b43d') {
      
      sh '''
        pwd
        ls
      '''
    }
  }
}