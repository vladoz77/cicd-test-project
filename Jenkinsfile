pipeline{
  agent{
    label "slave-node"
  }
  tools{
    jdk "Java17"
    maven "Maven3"
  }
  environment{
    APP_NAME = "cicd-test-project"
    RELEASE = "1.0.0"
    DOCKER_USER = "vladoz77"
    DOCKER_PASS = "dockerhub"
    IMAGE_NAME = '$(DOCKER_USER)' + '/' + '$(APP_NAME)'
    IMAGE_TAG = "\$(RELEASE)"-"${BUILD_NUMBER}"
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