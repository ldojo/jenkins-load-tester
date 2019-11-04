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
    stage('preamble') {
        steps {
            script {
                openshift.withCluster() {
                    openshift.withProject() {
                        echo "Using project: ${openshift.project()}"

                    }
                }
            }
        }
    }

    stage ('load-test'){
      steps {
        dir("single-pod-strategy"){
          sh 'mvn -B gatling:test'
          script{
            def reportDir = sh (
              script: 'find target/gatling  -maxdepth 1 -mindepth 1 -type d',
              returnStdout: true
            ).trim()
            echo pwd()
            archiveArtifacts artifacts: "${reportDir}/", fingerprint: true
            publishHTML (target: [
              allowMissing: false,
              alwaysLinkToLastBuild: false,
              keepAll: true,
              reportDir: "${reportDir}",
              reportFiles: 'index.html',
              reportName: "Gatling Report"
            ])
          }
        }
      }
    }
  }

//  post {
//    always {
//      archiveArtifacts artifacts: 'single-pod-strategy/target/gatling/', fingerprint: true
//    }
//  }
}
