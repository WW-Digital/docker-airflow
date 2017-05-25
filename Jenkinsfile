node {
  image = null
  tag = "1.8.1-${env.BUILD_NUMBER}"
  label = 'wwi-ca-airflow'

  stage("Checkout")
  checkout scm

  stage("Dockerize") {
    if (env.BRANCH_NAME == "master") {
      image = docker.build(label)
    } else {
      echo 'Not building image'
    }
  }

  stage("Push to repo") {
    if (env.BRANCH_NAME == "master") {
      docker.withRegistry('https://346405690657.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-cde-jenkins') {
        image.push(tag)
      }
    } else {
      echo 'No image to push'
    }
  }

  stage('Report') {
    if (currentBuild.currentResult != 'SUCCESS') {
      slackSend color: 'bad', message: "Build ${env.BUILD_NUMBER} failed for ${env.JOB_NAME} -- ${currentBuild.absoluteUrl}"

    } else if (currentBuild.previousBuild != null && currentBuild.previousBuild.currentResult != 'SUCCESS') {
      slackSend color: 'good', message: "${env.JOB_NAME} back to normal"

    } else {
      echo 'Nothing new to report'
    }
  }
}

