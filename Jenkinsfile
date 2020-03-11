#!groovy

properties([pipelineTriggers([pollSCM('H/5 * * * *')])])

pipeline {
   agent any
   stages {
      stage('CheckOut istio latest tag release') {
          steps {
              checkout(
                  [
                    $class: 'GitSCM', 
                    branches: [
                        [
                          name: "refs/tags/*"
                        ]
                    ], 
                    doGenerateSubmoduleConfigurations: false, 
                    extensions: [
                        [
                            $class: 'SubmoduleOption', 
                            disableSubmodules: false, 
                            parentCredentials: false, 
                            recursiveSubmodules: false, 
                            reference: '', 
                            trackingSubmodules: false
                        ]
                    ], 
                    submoduleCfg: [], 
                    userRemoteConfigs: [
                        [
                            url: "https://github.com/deepakdeore2004/my_first_project.git"
                        ]
                    ]
                  ]
              )
          }
      }

      stage('Cleanup old archives') {
          steps {
            echo "Cleaning up *.gz"
            sh 'rm -fv *.gz'
          }        
      }

      stage('Archive') {
        environment {
            // Get latest tag number
            TAG = sh(returnStdout: true, script: "git tag --sort version:refname | tail -1").trim()
        }          
         steps {
            echo "====== Checked out tag version: ${TAG} ======"
            // Archive gateway helm chart
            echo "Archiving the helm chart"
            sh '''
                cat README
                tar czf gw-${TAG}.tar.gz README jabberd_guide.pdf
                #install/kubernetes/helm/istio/charts/gateways
            '''
         }

         post {
            success {
               echo "Created the archive"
               sh "ls -lR"
            }
         }
      }
   }
}
