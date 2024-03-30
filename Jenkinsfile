pipeline {
  agent {
    kubernetes {
      yamlFile 'KubernetesPod.yaml'
    }
  }
  stages {

    stage('prepare') {
      steps {
        container('tools') {
          dir('project') {
            echo 'preparing the application'
            checkout([
              $class: 'GitSCM', 
              branches: [[name: '*/main']], 
              extensions: [], 
              userRemoteConfigs: [[url: 'https://github.com/rsmaxwell/example-java']]
            ])
            sh('./scripts/prepare.sh')
          }
        }
      }
    }

    stage('build') {
      steps {
        container('maven') {
          dir('project') {
            echo 'build the application'
            sh('./scripts/build.sh')

            echo 'testing the application'
            sh('./scripts/test.sh')

            echo 'packaging the application'
            sh('./scripts/package.sh')

            echo 'deploying the application'
            sh('./scripts/deploy.sh')
          }
        }
      }
    }
  }
}
