pipeline {
  agent any
    
  tools {nodejs "node"}
    
  stages {
        
    stage('checkout + Zip file') {
      steps {
        git 'https://github.com/bhimra/jenkins.git'
        sh 'zip -r nodejs2.zip /var/lib/jenkins/workspace/nodejs2/ '
      }
    }

    stage ('Prepare destination host') {
      steps {
        sh '''
          ssh -T centos@192.168.231.144 >> ENDSSH
          sudo chmod -R 775 /var/lib/jenkins/workspace/nodejs2.zip
          sudo cp -r /var/lib/jenkins/workspace/nodejs2/nodejs2.zip /home/centos/
          sudo mkdir /home/centos/logs
          sudo cd /home/centos/
          sudo npm install
          fi
ENDSSH
      '''
    }
  }
    stage ('Unzipping the files') {
      steps {
        sh '''
            ssh -t -t centos@192.168.231.144 'bash -s << 'ENDSSH'
            if [[ -d "/home/centos/*.zip" ]];
            then
                sudo unzip /home/centos/*.zip
            else
                sudo echo "unzip failure"
            fi
ENDSSH'
            '''
          }
        } 
            
    stage ('Verify node service') {
      steps {
        sh '''
          ssh -t -t  centos@192.168.231.144 'bash -s << 'ENDSSH'
          if [[ Z=$(sudo ps aux | grep -i [n]ode | awk 'NR==1' | gawk {'print $2'}) ]];
          then
              sudo cp -p /home/centos/nodejs2/index.js /home/nodejs2/logs/index.js.`date +%Y.%m.%d.%H.%M.%S`
              sudo kill -9 $Z
              echo "node service stop successfully."
              echo "restarting node service"
              cd /home/centos/
              sudo node index.js
              sudo ss -tnlp | grep "node"
          else
              echo "node service failed"
          fi
ENDSSH'
        '''
      }
    }  
  }
}
