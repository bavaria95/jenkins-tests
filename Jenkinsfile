node ('docker-slave') {
  stage('Build') {
      docker.withRegistry('https://gitlab-registry.cern.ch', 'ba4fc2f2-51c5-4a97-b1b2-b80ee5b2b43d') {

        git 'https://github.com/inspirehep/inspire-next.git'

        sh '''
          COMMITHASH=$(git rev-parse HEAD)
          git clone https://github.com/bavaria95/kubernetes-testing-deploy.git
          mv kubernetes-testing-deploy/Dockerfile .
          sed -i -e "s|COMMITHASH=HEAD|COMMITHASH=$COMMITHASH|g" kubernetes-testing-deploy/unified.sh
        '''

        env.COMMITHASH = sh returnStdout: true, script: 'git rev-parse HEAD | tr -d "\n"'

        // def newApp = docker.build("dpetruk/kubernetes-testing-deploy/inspire-base:${COMMITHASH}")
        // newApp.push()
      }
    }

  stage('Test') {
    withCredentials([file(credentialsId: 'K8S_CA', variable: 'K8S_CA'), file(credentialsId: 'K8S_CERT', variable: 'K8S_CERT'), file(credentialsId: 'K8S_CONFIG', variable: 'K8S_CONFIG'), file(credentialsId: 'K8S_KEY', variable: 'K8S_KEY')]) {

      def statusCode = sh returnStatus: true, script: '''
        sed -i -e "s|ca.pem|$K8S_CA|g" $K8S_CONFIG
        sed -i -e "s|cert.pem|$K8S_CERT|g" $K8S_CONFIG
        sed -i -e "s|key.pem|$K8S_KEY|g" $K8S_CONFIG
        export KUBECONFIG=$K8S_CONFIG
        ./kubernetes-testing-deploy/unified.sh
      '''
      if (statusCode == 0) {
          currentBuild.result = 'SUCCESS'
      }
      else {
          currentBuild.result = 'FAILURE'
      }
      
      println statusCode

      junit "result.xml"
    }
  }
}
