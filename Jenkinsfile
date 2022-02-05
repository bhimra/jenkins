pipeline {
  agent any
    
  tools {nodejs "node"}
    
  stages {
        
    stage('checkout + Zip file') {
      steps {
        git 'https://github.com/bhimra/jenkins.git'
        sh 'zip -r nodejs2.zip /var/lib/jenkins/workspace/nodejs2/'
        sh 'chmod -R 775 /var/lib/jenkins/workspace/nodejs2/nodejs2.zip'
        sh 'scp /var/lib/jenkins/workspace/nodejs2/nodejs2.zip centos@192.168.231.144:/home/centos/'
      }
    }
  
    stage ('Prepare destination host') {
      steps {
        sh '''
          ssh -t -t centos@192.168.231.144 >> ENDSSH
          sudo mkdir /home/centos/logs
          sudo mkdir /home/centos/NodeApp
          sudo chmod -R 775 /home/centos/NodeApp
          sudo chmod -R 775 /home/centos/logs
          sudo cd /home/centos/NodeApp
          sudo npm install
          fi
ENDSSH
      '''
    }
  }

    stage ('Verify node service') {
      steps {
        sh '''
          ssh -t -t  centos@192.168.231.144 'bash -s << 'ENDSSH'
          if [[ Z=$(sudo ps aux | grep -i [n]ode | awk 'NR==1' | gawk {'print $2'}) ]];
          then
              sudo kill -9 $Z
              echo "node service stop successfully."
              exit 0
          else
              echo "node service failed"
          fi
ENDSSH'
      '''
      }
    }

    stage ('Unzipping the files') {
        steps {
          sh '''
              ssh -t -t centos@192.168.231.144 'bash -s << 'ENDSSH'
              if [[ -d "/home/centos/NodeApp" ]];
              then
                  sudo mv /home/centos/NodeApp /home/centos/logs.`date +%Y.%m.%d.%H.%M.%S`
                  sudo unzip /home/centos/nodejs2.zip -d /home/centos/NodeApp
              else
                  sudo echo "unzip failure"
              fi
ENDSSH'
            '''
          }
        }

      stage ('Start the node service') {
      steps {
        sh '''
          ssh -t -t  centos@192.168.231.144 'bash -s << 'ENDSSH'
          cd /home/centos/NodeApp
          sudo node index.js > /dev/null 2>&1 <&- &
          X=$(curl -k  -o /dev/null -s -w %{http_code} http://192.168.231.144:3000)
          if [ $X == 200 ];
             then
                 echo -e 'web site is running'
              else
                 echo -e 'web site is down' 
          fi
ENDSSH'
        '''
      }
    }          
  }
}
