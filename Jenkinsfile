import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  echo 'inside getFTP'
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  echo 'after def pubprofile'
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=ae94812f-c5a2-4033-b929-c3e099e5d605',
        'AZURE_TENANT_ID=5068f31d-8fec-4149-b1ed-f52b84b4b010']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      echo 'Building....'
    }
  
    stage('deploy') {
      def resourceGroup = 'TestRG'
      def webAppName = 'jafarpythonwebapp'
      bat "echo webAppName=${webAppName}"
      // login Azure
      echo 'inside deploy'
      withCredentials([usernamePassword(credentialsId: 'jenkins2azure2', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
       //sh '''
        bat "az login --service-principal -u ${AZURE_CLIENT_ID} -p ${AZURE_CLIENT_SECRET} -t ${AZURE_TENANT_ID}"
        bat "az account set -s ${AZURE_SUBSCRIPTION_ID}"
        //'''
      }
      echo 'after login' 
      //get publish settings
      //def pubProfilesJson = '{"SQLServerDBConnectionString": "","controlPanelLink": "http://windows.azure.com"}'
      def pubProfilesJson = bat(script:"az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true)
      bat "echo -------------------------------------------"
      //bat "echo JSON=${pubProfilesJson}"
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      //upload package
      //sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      bat "az logout"
    }
  }
}
