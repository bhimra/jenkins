pipeline {
  agent any
    
  tools {nodejs "node"}
    
  stages {
        
    stage('checkout + Zip file') {
      steps {
        git 'https://github.com/bhimra/jenkins.git'
        sh 'chmod -R 775 /var/lib/jenkins/workspace/deployment'
        sh 'scp /var/lib/jenkins/workspace/deployment/index.js centos@192.168.231.144:/home/centos/deployment'
      }
    }
  }
}
