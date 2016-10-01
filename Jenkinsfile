#!groovy

node() {
    def stepsForParallel = [:]
    currentBuild.result = "SUCCESS"
    try {
       stage('Checkout') {
            checkout scm
            def names = nodeNames()
            def items - nodeItems()
            print "Name of Items: ${items}"
            print "Name of Nodes: ${names}"
       }
       stage('Print Env and Branch') {
            env.NODE_ENV = "test"
            print "Environment will be: ${env.NODE_ENV}"
            print "Job name: ${env.JOB_NAME}"
       }
       stage('Archive Artifacts') {
            archiveArtifacts artifacts: '**/Jenkinsfile', fingerprint: true
            sh 'ls -lha'
       }
       stage('Send Notice') {
            echo 'Send success email'
            mail body: "project ${env.JOB_NAME} build ${env.BUILD_NUMBER} successful: ${env.BUILD_URL}",
                 from: 'jenkins@saidsef.co.uk',
                 replyTo: 'jenkins@saidsef.co.uk',
                 subject: "project ${env.JOB_NAME} build ${env.BUILD_NUMBER} successful",
                 to: 'saidsef@gmail.com'
      }
    } catch (err) {
        currentBuild.result = "FAILURE"
            mail body: "project build error is here: ${env.BUILD_URL}" ,
                 from: 'jenkins@saidsef.co.uk',
                 replyTo: 'jenkins@saidsef.co.uk',
                 subject: "project ${env.JOB_NAME} build ${env.BUILD_NUMBER} failed:\n\n${err}",
                 to: 'saidsef@gmail.com'
        throw err
    } finally {
        // perform workspace cleanup only if the build have passed
        // if the build has failed, the workspace will be kept
        step([$class: 'WsCleanup', cleanWhenFailure: false])
    }
}

// This method collects a list of Node names from the current Jenkins instance
@NonCPS
def nodeNames() {
  return {
    jenkins.model.Jenkins.instance.nodes.collect {
       [ it.name ]
    }
  }
}

@NonCPS
def nodeItems() {
  return {
    jenkins.model.Jenkins.instance.items.each {
       [ it.name ]
    }
  }
}
