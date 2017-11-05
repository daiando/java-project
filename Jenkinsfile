pipeline {
  agent none

  environment {
    MAJOR_VERSION = 1
    DIST = "/var/www/html/rectangles/all/${env.BRANCH_NAME}"
    SERVER = "http://hoooober1.mylabserver.com/rectangles"
    LOCATION = "/var/www/html/rectangles"
  }

  stages {
    stage('Say Hello') {
      agent any

      steps {
        sayhello 'Awesome Student !!'
      }

    }
    stage('Unit Tests') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage('build') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f build.xml -v'
      }
      post {
        success {
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
      }
    }
    stage('deploy') {
      agent {
        label 'apache'
      }
      steps {
        sh "if ![ -d '${env.DIST}' ]; then mkdir ${env.DIST}; fi"
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar ${env.LOCATION}/all/${env.BRANCH_NAME}/"
      }
    }
    stage('Running on CentOS') {
      agent {
        label 'CentOS'
      }
      steps {
        sh "wget ${env.SERVER}/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage("Test on Debian") {
      agent {
        docker {
          image 'openjdk:8u121-jre'
          label 'CentOS'
        }
      }
      steps {
        sh "wget ${env.SERVER}/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage('Promote to Green') {
      agent {
        label 'apache'
      }
      when {
        branch 'master'
      }
      steps {
        sh "cp ${env.LOCATION}/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar ${env.LOCATION}/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      }
    }
    stage('Promote Dev branch to Master') {
      agent {
        label 'apache'
      }
      when {
        branch 'development'
      }
      steps {
        echo 'Stashing Any Local Changes now ...'
        sh 'git stash'
        echo 'Checking Out Development Branch'
        sh 'git checkout development'
        sh 'git pull origin development'
        echo 'Checking Out Master Branch'
        sh 'git checkout master'
        sh 'git pull origin master'
        echo 'Merging Development into Master'
        sh 'git merge development'
        echo 'Pushing to Origin Master'
        sh 'git push origin master'
        echo 'Tagging the Release'
        sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
      }
      post {
        success {
          emailext(
            subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Development Promoted to Master",
            body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Development Promoted to Master":</p>
            <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
            to: "daifloss@gmail.com"
          )
        }
      }
    }
  }
  post {
    failure {
      emailext(
        subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Failed !",
        body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed !":</p>
        <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
        to: "daifloss@gmail.com"
      )
    }
  }
}
