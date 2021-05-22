node {

  // config 
  def to = emailextrecipients([
          [$class: 'CulpritsRecipientProvider'],
          [$class: 'DevelopersRecipientProvider'],
          [$class: 'RequesterRecipientProvider']
  ])

  env.NODEJS_HOME = "${tool 'nodejs'}"
    // on linux / mac
  env.PATH="${env.NODEJS_HOME}/bin:${env.PATH}"
  sh 'npm --version'
  sh 'npm -g newman'
  sh 'newman -v'
  
  // job
  try {
    stage('build') {
      println('so far so good...')
    }
    stage('test') {
      println('A test has failed!')
      sh 'node --version;npm --version'
      sh 'newman run Api-mock-collection.postman_collection.json --suppress-exit-code 1'
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
