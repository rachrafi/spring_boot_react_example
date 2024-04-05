def registry = 'https://rachrafi.jfrog.io'
def imageName = 'rachrafi.jfrog.io/rachrafi-docker-local/ttrend'
def version   = '2.1.2'

pipeline {
  agent {
     node {
       label 'maven'
     }
  }

  environment {
      PATH = "/opt/apache-maven-3.9.6/bin:$PATH"
  }
    stages {
        stage("build"){
            steps {
                 echo "----------- build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                 echo "----------- build complted ----------"
            }
        }
        stage("test"){
            steps{
                echo "----------- Unit test started ----------"
                sh 'mvn surefire-report:report'
                 echo "----------- Unit test Completed ----------"
            }
        }
  /* 
  stage('SonarQube Analysis') {
    environment {
      scannerHome = tool 'sonar-scanner'
    }
    steps{
    withSonarQubeEnv('sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
      echo "----------- Sonar Scanner started ----------"
      sh "${scannerHome}/bin/sonar-scanner"
      echo "----------- Sonar Scanner Ended ----------"
    }
    }
  }
  stage("SonarQube Quality Gate") {
    steps {
        script {
          echo "----------- SonarQube Quality Gate Check Started ----------"
        timeout(time: 30, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
          // Add slight delay to circumvent quality gate race condition
          echo " ********* SLEEP FOR 15 SECONDS ********"
          sleep(time:15,unit:"SECONDS")

          def qualityGate = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
          if (qualityGate.status != 'OK') {
            error "Pipeline aborted due to quality gate failure: ${qualityGate.status}"
          }
        }
        echo "----------- SonarQube Quality Gate Check Ended ----------"
      }
    }
  }
  */
      stage("Upload Jar file to Artifactory") {
        steps {
            script {
                    echo '<--------------- Upload JAR to Artifactory --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"artifact-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "react-libs-snapshot/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Artifactory JAR Upload Ended --------------->'
            
            }
        }
      }

   /*  stage(" Docker Build ") {
      steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    } */

   /*  stage (" Docker Publish "){
        steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'
                docker.withRegistry(registry, 'artifact-cred'){
                    app.push()
                }
               echo '<--------------- Docker Publish Ended --------------->'
            }
        }
    } */
}
}
