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
              userRemoteConfigs: [[url: 'https://github.com/rsmaxwell/example-java-gradle']]
            ])
            sh('./scripts/prepare.sh')

          echo '*** debug (1) ***'
          sh('./scripts/debug.sh')
          }
        }
      }
    }

    stage('build') {
      steps {
        container('gradle') {

          echo '*** debug (2) ***'
          sh('id -a')
          sh('pwd')
          sh('ls -al')
          sh('set')

          echo '*** debug (3) ***'
          sh('./scripts/debug.sh')

          dir('project') {
            echo 'build the application'
            sh('./scripts/build.sh')

            echo 'testing the application'
            sh('./scripts/test.sh')

            echo 'packaging the application'
            sh('./scripts/package.sh')
          }
        }
      }
    }

    stage('deploy') {
      steps {
        container('maven') {
          dir('project') {
            echo 'deploying the application'
            sh('./scripts/deploy.sh')
          }
        }
      }
    }
  }
}
