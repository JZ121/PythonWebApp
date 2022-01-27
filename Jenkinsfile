import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
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
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'jenkins2azure', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
       sh '''
          az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
          az account set -s $AZURE_SUBSCRIPTION_ID
        '''
      }
      // get publish settings
      //def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      //def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      //sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}
