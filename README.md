# Jenkinsfile examples

This directory contains example Jenkinsfiles, which are used with the Pipeline Multibranch functionality, or the Pipeline script from SCM functionality.

## Jenkins Script Approval

You'll need to approve scripts to run this pipeline:

Script approval location: `http://<jenkins_host>:<port>/scriptApproval/`

### Scripts to approve

```
method hudson.model.AbstractCIBase getNodes
staticMethod jenkins.model.Jenkins getInstance
```
