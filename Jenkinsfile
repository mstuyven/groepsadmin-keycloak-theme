pipeline {
  agent any

  stages {
    stage('build') {
      steps {
        sh "rm -rf theme.zip"
        sh "npm --production prune"
        sh "npm --production install"
        sh "node_modules/.bin/bower prune --production"
        sh "node_modules/.bin/bower install --production"
        sh "cp bower_components/cleave.js/dist/cleave.min.js bower_components/cleave.js/dist/addons/cleave-phone.be.js bower_components/jquery/dist/jquery.min.js Scouts-en-Gidsen-Vlaanderen/login/resources/js"
        sh "zip -r theme.zip Scouts-en-Gidsen-Vlaanderen"
      }
    }

    stage('archive') {
      steps {
        archive 'theme.zip'

        script{
            def artifactory = Artifactory.server 'artifactory'

            def uploadSpec = '''{
              "files": [
                {
                  "pattern": "theme.zip",
                  "target": "groepsadmin-keycloak-theme/${BRANCH_NAME}/${BUILD_ID}/"
                }
             ]
            }'''

            def buildInfo = artifactory.upload spec: uploadSpec

            artifactory.publishBuildInfo buildInfo
        }
      }
    }
  }

  post {
    failure {
      emailen()
    }
    unstable {
      emailen()
    }
    changed {
      emailen()
    }
  }
}

def emailen() {
  mail(
    to: "tvl@scoutsengidsenvlaanderen.be,${env.CHANGE_AUTHOR_EMAIL}",
    subject: "[Jenkins] ${currentBuild.fullDisplayName} ${currentBuild.currentResult}",
    body: """${currentBuild.fullDisplayName} ${currentBuild.currentResult}

${currentBuild.absoluteUrl}"""
  )
}
