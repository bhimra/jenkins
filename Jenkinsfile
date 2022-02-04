pipeline {
  agent any
    
  tools {nodejs "node"}
    
  stages {
        
    stage('checkout + Zip file') {
      steps {
        git 'https://github.com/bhimra/jenkins.git'
        sh 'zip -r /var/lib/jenkins/workspace/nodejs2/*.js'
      }
    }

    stage ('Prepare destination host') {
      sh '''
        ssh -t -t centos@192.168.231.144 >> ENDSSH
        yum update -y
        mkdir /home/active
        cp -r /var/lib/jenkins/workspace/nodejs2/*.zip /home/active/
        chmod -R 775 /home/active/*
        cd /home/active/
        fi
ENDSSH
     '''
}

    stage ('Prepare destination host') {
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

            
    stage ('Verify node service  host') {
      sh '''
        ssh -t -t  centos@192.168.231.144 'bash -s << 'ENDSSH'
        if [[ Z=$(sudo ps aux | grep -i [n]ode | awk 'NR==1' | gawk {'print $2'}) ]];
        then
            cp -p /home/active/index.js /home/active/logs/index.js.`date +%Y.%m.%d.%H.%M.%S`
            kill -9 $Z
            echo "node service stop successfully."
        else
            echo "restarting node service"
            npm install
            sleep 5
            cd /home/active/
            node index.js
        fi
ENDSSH'
     '''
    }
  }
}