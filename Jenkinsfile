pipeline {
  agent any
    
  tools {nodejs "node"}
    
  stages {

    stage ('Prepare destination host') {
      steps {
        sh '''
          ssh -T centos@192.168.231.144 << ENDSSH
          mkdir /home/centos/rollback
          cd /home/centos/rollback/
ENDSSH
      '''
      }
    }

    stage('checkout + application scp') {
      steps {
        git 'https://github.com/bhimra/jenkins.git'
        sh 'sudo chmod -R 775 /var/lib/jenkins/workspace/rollback'
        sh 'scp /var/lib/jenkins/workspace/rollback/index2.js centos@192.168.231.144:/home/centos/rollback'
      }
    }

    stage ('Verify node service state') {
      steps {
        sh '''
          ssh -t -t  centos@192.168.231.144 'bash -s << 'ENDSSH'
          whoami
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
    
    stage ('Start the Rollback service') {
      steps {
        sh '''
        set -x
        ssh centos@192.168.231.144 "
                    sudo chmod -R 775 /home/centos/rollback/        
                    sudo node /home/centos/rollback/index2.js > /dev/null 2>&1 <&- & "
        X=$(curl -k  -o /dev/null -s -w %{http_code} http://192.168.231.144:3000)
        if [ $X -eq 200 ];
            then
                echo -e 'web site is running with version 2'
            else
                ssh centos@192.168.231.144 "
                    sudo chmod -R 775 /home/centos/deployment/
                    sudo node /home/centos/deployment/index.js > /dev/null 2>&1 <&- & "
        X=$(curl -k  -o /dev/null -s -w %{http_code} http://192.168.231.144:3000)
        if [ $X -eq 200 ];
            then
                echo -e 'web site is running with version 1'
            else
                echo -e 'web site is down with version 1' 
        fi '''
      }
    } 

    // stage ('Start the Rollback service') {
    //   steps {
    //     sh '''
    //     set -x
    //     ssh centos@192.168.231.144 "
    //                 sudo chmod -R 775 /home/centos/deployment/
    //                 sudo node /home/centos/deployment/index.js > /dev/null 2>&1 <&- & "
    //     X=$(curl -k  -o /dev/null -s -w %{http_code} http://192.168.231.144:3000)
    //     if [ $X -eq 200 ];
    //         then
    //             echo -e 'web site is running'
    //         else
    //             echo -e 'web site is down' 
    //     fi '''
    //   }
    // } 
  }
} 
