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
  
}
}
