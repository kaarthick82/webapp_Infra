// Jenkinsfile
String credentialsId = 'azuresp'

try {
  stage('checkout') {
    node {
         checkout scm
    }
  }

  // Run terraform init
  stage('init') {
   node {
    withCredentials([azureServicePrincipal(credentialsId: 'azuresp',
                                    subscriptionIdVariable: 'SUBS_ID',
                                    clientIdVariable: 'CLIENT_ID',
                                    clientSecretVariable: 'CLIENT_SECRET',
                                    tenantIdVariable: 'TENANT_ID')]) 
        {
          ansiColor('xterm') {
           sh 'terraform init'
          }
        }
    }
  }


  // Run terraform plan
  stage('plan') {
    node {
    withCredentials([azureServicePrincipal(credentialsId: 'azuresp',
                                    subscriptionIdVariable: 'SUBS_ID',
                                    clientIdVariable: 'CLIENT_ID',
                                    clientSecretVariable: 'CLIENT_SECRET',
                                    tenantIdVariable: 'TENANT_ID')]) 
        {
           sh 'terraform plan -var azure_client_id=${CLIENT_ID} -var azure_client_secret=${CLIENT_SECRET}'
          }
    }
  }

 if (env.BRANCH_NAME == 'master') {

 // Run terraform apply
 stage('apply') {
     node {
      withCredentials([azureServicePrincipal(credentialsId: 'azuresp',
                                   subscriptionIdVariable: 'SUBS_ID',
                                    clientIdVariable: 'CLIENT_ID',
                                   clientSecretVariable: 'CLIENT_SECRET',
                                    tenantIdVariable: 'TENANT_ID')])  { 
         ansiColor('xterm') {
                    sh 'terraform apply -auto-approve'
         }
       }
      }
    }

    // Run terraform show
    stage('show') {
      node {
         withCredentials([azureServicePrincipal(credentialsId: 'azuresp',
                                    subscriptionIdVariable: 'SUBS_ID',
                                    clientIdVariable: 'CLIENT_ID',
                                    clientSecretVariable: 'CLIENT_SECRET',
                                    tenantIdVariable: 'TENANT_ID')])  { 
           ansiColor('xterm') {
                    sh 'terraform refresh'
           }}
      }
    }
	
    // Run terraform destroy
//    stage('destroy') {
//      node {
//         withCredentials([azureServicePrincipal(credentialsId: 'azuresp',
//                                    subscriptionIdVariable: 'SUBS_ID',
//                                    clientIdVariable: 'CLIENT_ID',
//                                    clientSecretVariable: 'CLIENT_SECRET',
//                                    tenantIdVariable: 'TENANT_ID')])  { 
//           ansiColor('xterm') {
//                    sh 'terraform destroy -var azure_client_id=${CLIENT_ID} -var azure_client_secret=${CLIENT_SECRET} -auto-approve'
//           }}
//      }
//    }
 stage ('appdeploy'){
   node{
   ansiColor('xterm'){
     sh '> /var/lib/jenkins/.ssh/known_hosts'
     sh '> /root/.ssh/known_hosts'
     sh 'ansible-playbook /etc/ansible/nginx.yml --extra-vars "version=v1.0"'
   }
 }
 }
 
 }
  currentBuild.result = 'SUCCESS'
}
catch (org.jenkinsci.plugins.workflow.steps.FlowInterruptedException flowError) {
  currentBuild.result = 'ABORTED'
}
catch (err) {
  currentBuild.result = 'FAILURE'
  throw err
}
finally {
  if (currentBuild.result == 'SUCCESS') {
    currentBuild.result = 'SUCCESS'
  }
}
