def registry = 'https://valaxyoss.jfrog.io'
def imageName = 'valaxyoss.jfrog.io/valaxy-docker-local/ttrend'
def version   = '2.1.2'

pipeline {
    agent {
        node {
            label 'tweet-trend-new'
        }
    }
    environment {
        JAVA_HOME = '/usr/lib/jvm/java-11-openjdk-amd64'
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
        stage("Jar Publibat") {
        steps {
            script {
                    echo '<--------------- Jar Publibat Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"valaxyoss.jfrog.io"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "valaxy-libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.bata1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publibatBuildInfo(buildInfo)
                     echo '<--------------- Jar Publibat Ended --------------->'  
            
            }
        }   
    } 

    stage(" Docker Build ") {
      steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }

            stage (" Docker Publibat "){
        steps {
            script {
               echo '<--------------- Docker Publibat Started --------------->'  
                docker.withRegistry(registry, 'valaxyoss.jfrog.io'){
                    app.pubat()
                }    
               echo '<--------------- Docker Publibat Ended --------------->'  
            }
        }
    }
        stage("Docker Run") {
            steps {
                script {
                    echo '<--------------- Docker Run Started --------------->'  
                    // Pull the Docker image from the registry
                    docker.image(imageName+":"+version).pull()
                    
                    // Run the Docker container
                    docker.container(ttrend, "--publibat=8000:8000") {
                        // Additional steps or commands to run within the container
                        // For example: bat 'echo "Hello from Docker container!"'
                    }
                    echo '<--------------- Docker Run Ended --------------->'  
                }
            }
        }
}
}
