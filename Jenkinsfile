pipeline {
  agent any
    
  tools {nodejs "node"}
    
  stages {
        
    stage('checkout + Zip file') {
      steps {
        git 'https://github.com/bhimra/jenkins.git'
        sh 'cd /var/lib/jenkins/workspace/'
        sh 'sudo cp -r /var/lib/jenkins/workspace/nodejs2@2/ /tmp/'
        sh 'cd /tmp/'
        sh 'sudo chown -R admin:admin /tmp/nodejs2@2/'
        sh 'sudo zip -r nodejs2.zip /tmp/nodejs2@2/'
        sh 'scp /tmp/nodejs2@2/nodejs2.zip centos@192.168.231.144:/home/centos/'
      }
    }
  
    stage ('Prepare destination host') {
      steps {
        sh '''
          ssh -T centos@192.168.231.144 << ENDSSH
          sudo mkdir /home/centos/logs
          sudo mkdir /home/centos/NodeApp
          sudo chmod -R 775 /home/centos/NodeApp
          sudo chmod -R 775 /home/centos/logs
          sudo cd /home/centos/NodeApp
          sudo dnf install npm -y
          npm install
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
                sudo mv /home/centos/NodeApp/ /home/centos/logs.`date +%Y.%m.%d.%H.%M.%S`
                sudo unzip /home/centos/nodejs2.zip -d /home/centos/NodeApp
                sudo chmod -R 775 /home/centos/NodeApp/*
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
          ssh -T centos@192.168.231.144 << ENDSSH
          cd /home/centos/NodeApp/tmp/nodejs2@2/
          sudo node index.js > /dev/null 2>&1 <&- &
ENDSSH
        '''
      }
    }
    
    stage ('verify the service on port 3000 from Jenkins server') {
      steps {
        sh '''
          cd /opt/
          ./test.sh
ENDSSH
        '''
      }
    }
  }
}
