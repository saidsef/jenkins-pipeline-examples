#!groovy

node() {
    currentBuild.result = "SUCCESS"
    try {
       stage 'Checkout'
            checkout scm
            def names = nodeNames()
            print "Name of Nodes: ${names}"
       stage 'Print Env and Branch'
            env.NODE_ENV = "test"
            print "Environment will be : ${env.NODE_ENV}"
            print "Branch name: ${env.BRANCH_NAME}"
       stage 'Send Notice'
            echo 'Send success email'
            mail body: 'project build successful',
                        from: 'saidsef@gmail.com',
                        replyTo: 'saidsef@gmail.com.com',
                        subject: 'project build successful',
                        to: 'saidsef@gmail.com'
        }
    catch (err) {
        currentBuild.result = "FAILURE"
            mail body: "project build error is here: ${env.BUILD_URL}" ,
            from: 'saidsef@gmail.com',
            replyTo: 'saidsef@gmail.com',
            subject: 'project build failed',
            to: 'saidsef@gmail.com'
        throw err
    }
}

// This method collects a list of Node names from the current Jenkins instance
@NonCPS
def nodeNames() {
  return jenkins.model.Jenkins.instance.nodes.collect { node -> node.name }
}
