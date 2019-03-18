pipeline {
  agent any
  stages {
    stage('checkout') {
      steps {
        git(url: 'https://github.com/narendrasingamaneni91/QshoreDevOpsSBI.git', branch: 'master', credentialsId: 'narendrasingamaneni91')
      }
    }
  }
}