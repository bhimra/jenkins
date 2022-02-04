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
          sudo yum install unzip -y
          sudo firewall-cmd --add-port={8080,3000}/tcp --permanent
          sudo firewall-cmd --reload
          sudo mkdir /home/active
          sudo chmod -R 775 /home/active
          sudo cp -r /var/lib/jenkins/workspace/nodejs2.zip /home/active/
          sudo chmod -R 775 /home/active/*
          sudo cd /home/active/
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
            if [[ -d "/home/active/*.zip" ]];
            then
                sudo unzip /home/active/*.zip
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
              sudo cp -p /home/active/index.js /home/active/logs/index.js.`date +%Y.%m.%d.%H.%M.%S`
              sudo kill -9 $Z
              echo "node service stop successfully."
              echo "restarting node service"
              cd /home/active/
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
