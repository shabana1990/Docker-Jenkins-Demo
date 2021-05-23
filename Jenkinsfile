pipeline {

  environment {

    registry = "shabana1990/pythonrepository_01"

    registryCredential = 'docker-creds'

    dockerImage = ''

  }

  agent any

  stages {

    stage('Cloning Git') {

      steps {

        git 'https://github.com/shabana1990/Docker-Jenkins-Demo'

      }

    }

    stage('Building image') {

      steps{

        script {

          dockerImage = docker.build registry + ":$BUILD_NUMBER"

        }

      }

    }
	  

    stage('Deploy Image') {

      steps{

        script {

          docker.withRegistry( '', registryCredential ) {

            dockerImage.push()

          }

        }

      }

    }
	stage('Remove Existing Container') {

      steps{
	  script {
			sh '''

			a="$(docker container ls --format="{{.ID}}\t{{.Ports}}" | grep "8000" | awk '{print $1}')"

			echo $a

			if [ -z "$a" ]
			then
			echo "do not delete"
			else
			docker rm -f $a
			fi

			'''
		}
      }

    }
	
    
    stage('Dangling Containers') {
      sh 'docker ps -q -f status=exited | xargs --no-run-if-empty docker rm'
    }

    stage('Dangling Images') {
      sh 'docker images -q -f dangling=true | xargs --no-run-if-empty docker rmi'
    }
    
    stage('Dangling Volumes') {
      sh 'docker volume ls -qf dangling=true | xargs -r docker volume rm'
    }
  
    
    stage('Run Docker Image in Lab') {

      steps{

        script {

		        sh "docker run -d -p 8000:8000 ${dockerImage.imageName()}"
        }

      }

	}

  }

}
