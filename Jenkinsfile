#!groovy

import groovy.json.JsonOutput
import jenkins.model.*

node {
    def stepsForParallel = [:]
    currentBuild.result = "SUCCESS"
    env.BUILD_MSG = ''
    try {
       stage('Checkout') {
          checkout scm
          def names = nodeNames()
          print "Name of Nodes: ${names}"
       }
       stage('Print Env and Branch') {
          env.NODE_ENV = "test"
          print "Environment will be: ${env.NODE_ENV}"
          print "Job name: ${env.JOB_NAME}"
       }
       stage('Archive Artifacts') {
          archiveArtifacts([artifacts: '**/Jenkinsfile', fingerprint: true, onlyIfSuccessful: true])
          stash(allowEmpty: false, includes: 'Jenkinsfile', name: 'pipeline-sample')
       }
       stage('Send Notice') {
          echo 'Send success email'
       }
    } catch (err) {
       currentBuild.result = "FAILURE"
       env.BUILD_MSG = err
       throw err
    } finally {
        // perform workspace cleanup only if the build have passed
        // if the build has failed, the workspace will be kept
        step([$class: 'WsCleanup', cleanWhenFailure: false])
        def payload =  env.BUILD_MSG
        sendEmail()
        def options = JsonOutput.toJson([
          result: currentBuild.result,
          number: env.BUILD_NUMBER,
          payload: [reason: env.BUILD_CAUSE, body: payload]
        ])
        postToES(options)
    }
}

// This method collects a list of Node names from the current Jenkins instance
@NonCPS
def nodeNames() {
  return {
    Jenkins.instance.nodes.collect { i ->
      [ i.name ]
    }
  }
}

def postToES(options) {
    print "${options}"
    sh "curl -XPOST http://localhost:9200/jenkins/jobs/${env.BUILD_NUMBER} -d '${options}' -s || true"
}

def sendEmail() {
  return {
    mail body: "project build ${currentBuild.result} is here: ${env.BUILD_URL}" ,
        from: "${env.EMAIL_REPO}",
        replyTo: "${env.EMAIL_REPO}",
        subject: "${currentBuild.result}: project ${env.JOB_NAME} build ${env.BUILD_NUMBER}",
        to: "${env.EMAIL_PROJECT}"
  }
}
