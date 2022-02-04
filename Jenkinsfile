pipeline {
  agent any
    
  tools {nodejs "node"}
    
  stages {
        
    stage('checkout + Zip file') {
      steps {
        git 'https://github.com/bhimra/jenkins.git'
        sh 'zip /var/lib/jenkins/workspace/nodejs2'
      }
    }

    stage ('Prepare destination host') {
      steps {
        sh '''
          ssh -t -t centos@192.168.231.144 >> ENDSSH
          yum update -y
          yum install unzip -y
          firewall-cmd --add-port={8080,3000}/tcp --permanent
          firewall-cmd --reload
          mkdir /home/active
          cp -r /var/lib/jenkins/workspace/nodejs2.zip /home/active/
          chmod -R 775 /home/active/*
          cd /home/active/
          npm install
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
                unzip /home/active/*.zip
            else
                echo "unzip failure"
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
              cp -p /home/active/index.js /home/active/logs/index.js.`date +%Y.%m.%d.%H.%M.%S`
              kill -9 $Z
              echo "node service stop successfully."
              echo "restarting node service"
              cd /home/active/
              node index.js
              ss -tnlp | grep "node"
          else
              echo "node service failed"
          fi
ENDSSH'
        '''
      }
    }  
  }
}
