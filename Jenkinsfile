pipeline {
  agent {
    kubernetes {
      yamlFile 'KubernetesPod.yaml'
    }
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
  }

  environment {
    FAMILY = 'linux'
    ARCHITECTURE = 'amd64'
  }

  stages {

    stage('prepare') {
      steps {
        container('tools') {
          dir('project') {
            echo "preparing the application (FAMILY=${env.FAMILY}, ARCH=${env.ARCHITECTURE})"
            checkout([
              $class: 'GitSCM', 
              branches: [[name: '*/main']], 
              extensions: [[
                $class: 'CloneOption',
                noTags: false,   // <-- key bit: fetch tags
                shallow: false,
                depth: 0,
                timeout: 10
              ]],
              userRemoteConfigs: [[url: 'https://github.com/rsmaxwell/example-gradle']]
            ])
            sh('./scripts/prepare.sh')
          }
        }
      }
    }

    stage('deploy') {
      steps {
        container('gradle') {
          dir('project') {
            withEnv([
              'GRADLE_USER_HOME=/home/gradle/.gradle',
              'HOME=/home/gradle'
            ]) {
              echo 'deploying the application'
              sh('./scripts/deploy.sh')
            }
          }
        }
      }
    }
  }
}
