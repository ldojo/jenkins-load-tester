pipeline {
  agent {
    node {
      label 'maven'
    }
  }

  parameters {
    string(defaultValue: 20, description: 'time in minutes for the timeout of this jenkins job', name: 'timeout')
  }

  options {
    timeout(time: ${params.timeout}, unit: 'MINUTES')
  }

  stages {
    stage ('load-test'){
      steps {
          sh 'mvn -B gatling:test'
      }
    }
    stage ('publish-report'){
      script{
        def reportDir = sh (
          script: 'find target/gatling  -maxdepth 1 -mindepth 1 -type d',
          returnStdout: true
        ).trim()
        archiveArtifacts artifacts: "${reportDir}/", fingerprint: true
        publishHTML (target: [
          allowMissing: false,
          alwaysLinkToLastBuild: false,
          keepAll: true,
          reportDir: "${reportDir}",
          reportFiles: 'index.html',
          reportName: "Gatling Report"
        ])
        echo 'You can view the Gatling HTML report for this build. It is named "Gatling Report". The HTML files for the report are also saved as an artifact of this build.'
      }
    }
  }
}
