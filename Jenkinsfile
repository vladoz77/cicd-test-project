pipeline{
  agent{
    label "slave-node"
  }
  tools{
    jdk "Java17"
    maven "Maven3"
  }
  stages{
    stage("Clean-workspace"){
        steps{
            // Clean before build
            cleanWs()
        }
    }
    stage("checkout from scm"){
        steps{
          git branch: 'main', url: 'https://github.com/vladoz77/cicd-test-project'
        }
    }

    stage("Build Application"){
      steps{
        sh "mvn clean install"
      }
    }

    stage("Test Application"){
      steps{
        sh "mvn test"
      }
    }

    stage("sonarqube analyses"){
      steps{
        def scannerHome = tool 'sonar-scanner'; 
        withSonarQubeEnv(credentialsId: 'sonarqube-token', installationName: 'sonarqube-server'){
          sh "mvn sonar:sonar"
        }
      }
    }
  }
}