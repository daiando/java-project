pipeline {
  agent none

  stages {
    stage('Unit Tests') {
      agent {
        label 'master'
      }
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage('build') {
      agent {
        label 'master'
      }
      steps {
        sh 'ant -f build.xml -v'
      }
    }
    stage('deploy') {
      agent {
        label 'master'
      }
      steps {
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
      }
      post {
        success {
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
      }
    }
    stage('Running on CentOS') {
      agent {
        label 'CentOS'
      }
      steps {
        sh "wget http://hoooober1.mylabserver.com/rectangles/all/rectancle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
  }
}
