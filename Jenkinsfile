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
        script{
          withSonarQubeEnv(credentialsId: 'sonarqube-token'){
              sh "mvn sonar:sonar"
          }
        }
      }
    }

    stage("Quality gate"){
      steps{
        script{
          waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-token'
        }
      }
    }
    
    stage("Build and push Docker image"){
      steps{
        script{
          docker.withRegistry('',DOCKER_PASS){
            docker_image = docker.build "${IMAGE_NAME}"
          }
          
          docker.withRegistry('',DOCKER_PASS){
            docker_image.push("${IMAGE_TAG}")
            docker_image.push('latest')
          }
        }
      }
    }

  }
}