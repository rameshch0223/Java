pipeline {
    agent none

      options {
        buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
      }
    stages {
      stage('Unit Tests'){
        agent{
          label 'apache'
        }
        steps{
          sh 'ant -f test.xml -v'
          junit 'reports/result.xml'
        }

      }
      stage('Build'){
        agent{
          label 'apache'
        }
        steps{
        sh 'ant -f build.xml -v'
        }
        post {
          success{
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
          }
        }
      }

      stage('deploy'){
        agent {
          label 'apache'
        }
        steps{
          sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
          sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
        }

      }
      stage ('running on CentOS'){
        agent {
          label 'CentOS'
        }
        steps{
          sh "wget http://uday7g3.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
          sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
        }

      }
      stage('promote to green'){
        agent {
          label 'apache'
        }
        when {
          branch 'master'
        }
        steps{
          sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar  /var/www/html/rectangles/green/"
        }
      }
      stage('promote development branch to master') {
        agent{
          label 'apache'
        }
         when {
           brach 'development'
         }
         steps{
           echo " Stashing any Local Changes"
           sh "git stash"
           echo "Checking out Development Branch"
           sh "git checkout development"
           echo "checking out Master branch"
           sh "git checkout master"
           echo "merging development Branch into Master"
           sh "git merge development"
           echo "pushing to Origin master"
           sh "git push origin master"
         }
      }
     }

}
