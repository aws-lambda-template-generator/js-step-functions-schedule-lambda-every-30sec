// If library is needed, add library
// @Library('JENKINS_COMMONS') _

pipeline {
  agent any

  environment {
    REGION = '<aws region>'
    NON_PROD_ACCOUNT_ID = '<non prod account id>'
    PROD_ACCOUNT_ID = '<prod account id>'
    ROLE_NAME = "<role name to deploy lambda, like jenkin_deploy>"
    NODE_NAME = "<node version such as nodejs-10.9.0>"
  }

  parameters {
    string (name: 'targetEnv', defaultValue: 'nonprod', description: 'The environment to which to deploy: nonprod, preprod, prod.')
  }

  stages {
 
    stage('Initialise') {
      steps {
        echo "Delolying the lambda function from ${GIT_BRANCH} into ${params.targetEnv}" 
        echo "Git branch: ${GIT_BRANCH}"
        echo "Env workspace: ${WORKSPACE}"
        echo "Build url: ${BUILD_URL}"
        echo "Build tag: ${BUILD_TAG}"
        echo "NodeJS plugin name: ${NODE_NAME}"
      }
    } // end of initialise stage

    stage('Install') {
      steps {
        echo 'Installing dependencies...'
        nodejs("${NODE_NAME}") {
          echo 'Installing project dependencies'
          sh 'npm i'
        }
      }
    }

    stage('Unit Tests') {
      steps {
        echo 'Running unit tests...'
        nodejs("${NODE_NAME}") {
          sh 'npm test'
        }
      }
    } // end of unit test stage

    stage('Integration Tests') {
      steps {
        echo 'Running integration tests...'
        nodejs("${NODE_NAME}") {
          withIAMRole(env.NON_PROD_ACCOUNT_ID ,env.REGION , env.ROLE_NAME) {
            sh 'npm run integration'
          }
        }
      }
    } // end of integration test stage

    stage('Nonprod Deploy') {
     
      when {
        expression { return params.targetEnvironment != 'prod'}
      }
      steps {
        echo "Deploying into ${params.targetEnv}...."
        nodejs("${NODE_NAME}") {         
          withIAMRole(env.NON_PROD_ACCOUNT_ID ,env.REGION , env.ROLE_NAME) {
            // sh 'sls create_domain --stage ' + params.targetEnv          
            sh 'npm run deploy -- --stage ' + params.targetEnv + ' --account ' + env.NON_PROD_ACCOUNT_ID
          }
        }
      } // end of prd1 steps     
    } // end of dev deploy stage

    stage('Preprod Deploy') {
      when {
        expression { return params.targetEnvironment == 'prod' }
      }
      steps {
        echo "Production Deployment...."
        nodejs("${NODE_NAME}") {
          withIAMRole(env.PROD_ACCOUNT_ID ,env.REGION , env.ROLE_NAME) {
            // sh 'sls create_domain --stage ' + params.targetEnv
            sh 'npm run deploy -- --stage preprod --account ' + env.PROD_ACCOUNT_ID
          }
        }
      } // end steps
    } // end of preprod stage
    stage('Prod Deploy') {
      when {
        expression { return params.targetEnvironment == 'prod' }
      }
      steps {
        echo "Production Deployment...."
        nodejs("${NODE_NAME}") {
          withIAMRole(env.PROD_ACCOUNT_ID ,env.REGION , env.ROLE_NAME) {
            // sh 'sls create_domain --stage ' + params.targetEnv
            sh 'npm run deploy -- --stage ' + params.targetEnv+ ' --account ' + env.PROD_ACCOUNT_ID

          }
        }
      } // end steps
    } // end of prod deploy stage
  } // end of stages
} // end of pipeline
