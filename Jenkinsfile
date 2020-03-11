#!groovy

properties([pipelineTriggers([pollSCM('H H * * *')])])

pipeline {
   agent any
   
   stages {
       
      stage('CheckOut istio latest release') {
          steps {
              // "variable" scope is local in environment block, "script" makes the variable global
              script {
                TAG = sh(returnStdout: true, script: "curl --silent https://api.github.com/repos/istio/istio/releases/latest | jq -r .tag_name").trim()
              }
              checkout(
                  [
                    $class: 'GitSCM', 
                    branches: [
                        [
                          name: "refs/tags/${TAG}"
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
                            url: "https://github.com/istio/istio.git"
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
         steps {
            echo "====== Checked out tag version: ${TAG} ======"
            // Archive gateway helm chart
            echo "Archiving the helm chart"
            sh """
                tar czf gateway-${TAG}.tar.gz -C install/kubernetes/helm/istio/charts/ gateways
            """
         }

         post {
            success {
               echo "Created the archive"
               sh "ls -l"
            }
         }
      }
   }
}
