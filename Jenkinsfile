node {

  // config 
  def to = emailextrecipients([
          [$class: 'CulpritsRecipientProvider'],
          [$class: 'DevelopersRecipientProvider'],
          [$class: 'RequesterRecipientProvider']
  ])

  
  // job
  try {
    stage('git') {
      checkout scm
    }
    stage('Newman setup') {
       env.NODEJS_HOME = "${tool 'nodejs'}"
    // on linux / mac
       env.PATH="${env.NODEJS_HOME}/bin:${env.PATH}"
       sh 'npm --version'
       sh 'npm install -g newman'
       sh 'newman -v'
    }
    stage('test') {
      println('Initiating tests through newman')
      sh 'pwd'
      sh 'newman run ./Api-mock-collection.postman_collection.json --suppress-exit-code -1'
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
