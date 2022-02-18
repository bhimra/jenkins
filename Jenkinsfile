pipeline {
  agent any
    
  tools {nodejs "node"}
    
  stages {

    stage ('Prepare destination host') {
      steps {
        sh '''
          ssh -T centos@192.168.231.144 << ENDSSH
          sudo mkdir /home/centos/deployment/
          sudo chmod -R 775 /home/centos/deployment
          sudo cd /home/centos/deployment/
          sudo dnf install npm -y
          npm install
ENDSSH
      '''
      }
    }

    stage('checkout + application scp') {
      steps {
        git 'https://github.com/bhimra/jenkins.git'
        sh 'chmod -R 775 /var/lib/jenkins/workspace/deployment'
        sh 'scp /var/lib/jenkins/workspace/deployment/index.js centos@192.168.231.144:/home/centos/deployment/'
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
    
//     stage ('Start the node service') {
//       steps {
//         sh '''
//           ssh -t -t  centos@192.168.231.144 'bash -s << 'ENDSSH'
//           sudo chmod -R 775 /home/centos/deployent/var/lib/jenkins/workspace/*
//           sudo chmod -R 775 /home/centos/NodeApp/var/lib/jenkins/workspace/nodejs2/*
//           cd /home/centos/NodeApp/var/lib/jenkins/workspace/nodejs2/
//           sudo node index.js > /dev/null 2>&1 <&- &
//           X=$(curl -k  -o /dev/null -s -w %{http_code} http://192.168.231.144:3000)
//           if [ $X = 200 ];
//              then
//                  echo -e 'web site is running'
//               else
//                  echo -e 'web site is down' 
//           fi
// ENDSSH'
//         '''
//       }
//     }            
  }
}
