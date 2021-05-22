node {

  // config 
  def to = emailextrecipients([
          [$class: 'CulpritsRecipientProvider'],
          [$class: 'DevelopersRecipientProvider'],
          [$class: 'RequesterRecipientProvider']
  ])

  // job
  try {
    stage('build') {
      println('so far so good...')
    }
    stage('test') {
      println('A test has failed!')
      sh 'export PATH=$PATH:/home/osboxes/.nvm/versions/node/v14.17.0/bin; echo $PATH; /home/osboxes/.nvm/versions/node/v14.17.0/bin/newman run Api-mock-collection.postman_collection.json --suppress-exit-code 1'
      sh 'echo $PATH'
    }
  } catch(e) {
    // mark build as failed
    currentBuild.result = "FAILURE";
    // set variables
    def subject = "${env.JOB_NAME} - Build #${env.BUILD_NUMBER} ${currentBuild.result}"
    def content = '${JELLY_SCRIPT,template="html"}'

    // send email
    if(to != null && !to.isEmpty()) {
      emailext(body: content, mimeType: 'text/html',
         replyTo: '$DEFAULT_REPLYTO', subject: subject,
         to: '$DEFAULT_REPLYTO', attachLog: true )
    }

    // mark current build as a failure and throw the error
    throw e;
  }
}
