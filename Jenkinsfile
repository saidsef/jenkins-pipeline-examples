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
          archiveArtifacts([artifacts: '**/Jenkinsfile', fingerprint: true])
          sh 'ls -lha'
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
        def options = [
          result: currentBuild.result,
          number: env.BUILD_NUMBER,
          payload: [reason: env.BUILD_CAUSE, body: payload]
        ]
        print JsonOutput.toJson(options)
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
  return {
    print "${options.result}"
    print "${options.number}"
    print "${options.payload}"
    sh "curl -XPOST http://localhost:9200/jenkins/${options.result}/${options.number} -d '{ ${options.payload} }'"
    print "sent data to local ES"
  }
}

def sendEmail() {
  return {
    mail body: "project build ${currentBuild.result} is here: ${env.BUILD_URL}" ,
        from: 'jenkins@saidsef.co.uk',
        replyTo: 'jenkins@saidsef.co.uk',
        subject: "${currentBuild.result}: project ${env.JOB_NAME} build ${env.BUILD_NUMBER}",
        to: 'said@saidsef.com'
  }
}
