pipeline {
    agent {
        node {
            label 'tweet-trend-new'
        }
    }

    stages {
        stage("build"){
            steps {
                 echo "----------- build started ----------"
                bat 'mvn clean deploy -Dmaven.test.skip=true'
                 echo "----------- build complted ----------"
            }
        }
        stage("test"){
            steps{
                echo "----------- unit test started ----------"
                bat 'mvn surefire-report:report'
                 echo "----------- unit test Complted ----------"
            }
        }

    stage('SonarQube analysis') {
    environment {
      scannerHome = tool 'valaxy-sonar-scanner'
    }
    steps{
    withSonarQubeEnv('valaxy-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
      bat "${scannerHome}/bin/sonar-scanner"
    }
    }
  } 
}
}
