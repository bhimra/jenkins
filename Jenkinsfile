pipeline {
  agent any
    
  tools {nodejs ""}
    
  stages {
        
    stage('Git') {
      steps {
        git 'https://github.com/bhimra/jenkins.git'
      }
    }
     
    stage('Build') {
      steps {
        sh 'npm install'
      }
    }  
    
            
    stage('Test') {
      steps {
        sh 'node index.js'
      }
    }
  }
}
