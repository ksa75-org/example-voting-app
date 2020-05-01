pipeline {

   agent none
   stages {
      stage('worker build') {
        agent {
         docker {
           image 'maven:3-alpine'
           args '-v /srv/jenkins/jenkins_home/.m2:/root/.m2  -v /srv/jenkins/jenkins_home/settings.xml:/my/settings.xml:ro --env JAVA_OPTS="-Dhttp.proxyHost=172.17.7.137 -Dhttp.proxyPort=3129 -Dhttps.proxyHost=172.17.7.137 -Dhttps.proxyPort=3129" --env http_proxy="http://172.17.7.137:3129/" --env  https_proxy="http://172.17.7.137:3129/"'
           }
         }
         when{
           changeset "**/worker/**"
         }
         steps {
            echo 'Compiling worker app'
            dir('worker'){
              sh 'mvn -s /my/settings.xml compile'
            }
         }
      }
      
      stage('worker test') {
        agent {
         docker {
           image 'maven:3-alpine'
           args '-v /srv/jenkins/jenkins_home/.m2:/root/.m2  -v /srv/jenkins/jenkins_home/settings.xml:/my/settings.xml:ro --env JAVA_OPTS="-Dhttp.proxyHost=172.17.7.137 -Dhttp.proxyPort=3129 -Dhttps.proxyHost=172.17.7.137 -Dhttps.proxyPort=3129" --env http_proxy="http://172.17.7.137:3129/" --env  https_proxy="http://172.17.7.137:3129/"'
           }
         }        
         when{
          changeset "**/worker/**"
         }
         steps {
            echo 'Running Unit Tests on worker app'
            dir('worker'){
              sh 'mvn -s /my/settings.xml clean test'
            }            
         }
      }
      
      stage('worker package') {
        agent {
         docker {
           image 'maven:3-alpine'
           args '-v /srv/jenkins/jenkins_home/.m2:/root/.m2  -v /srv/jenkins/jenkins_home/settings.xml:/my/settings.xml:ro --env JAVA_OPTS="-Dhttp.proxyHost=172.17.7.137 -Dhttp.proxyPort=3129 -Dhttps.proxyHost=172.17.7.137 -Dhttps.proxyPort=3129" --env http_proxy="http://172.17.7.137:3129/" --env  https_proxy="http://172.17.7.137:3129/"'
           }
         }        
         when{
           branch "master"
           changeset "**/worker/**"
         }
         steps {
            echo 'Building worker app'
            dir('worker'){
              sh 'mvn -s /my/settings.xml package -Dskiptests'
            }            
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
         }
      }

      stage('worker-docker-package') {
         agent any
         when{
           changeset "**/worker/**"
           branch 'master'
         }
         steps {
            echo 'Building worker app with docker'
            script{
              docker.withRegistry('https://index.docker.io/v1/','dockerhublogin'){
                def workerImage = docker.build("ksa75/worker:v${env.BUILD_ID}","./worker")
                workerImage.push()
                workerImage.push("${env.BRANCH_NAME}")

              }
            }
         }
      }

      stage('result build') {
         agent {
             docker {
                 image 'node:8.16.0-alpine'
                 args '--env http_proxy="http://172.17.7.137:3129/" --env  https_proxy="http://172.17.7.137:3129/"'
               }
         }
         when{
           changeset "**/result/**"
         }
         steps {
            echo 'Compiling result app'
            dir('result'){
              sh 'npm install'
            }
         }
      }
      
      stage('result test') {
         agent {
             docker {
                 image 'node:8.16.0-alpine'
                 args '--env http_proxy="http://172.17.7.137:3129/" --env  https_proxy="http://172.17.7.137:3129/"'
               }
         }
         when{
          changeset "**/result/**"
         }
         steps {
            echo 'Running Unit Tests on result app'
            dir('result'){
              sh 'npm install'
              sh 'npm test'
            }            
         }
      }
      stage('result-docker-package') {
         agent any
         when{
           changeset "**/result/**"
           branch 'master'
         }
         steps {
            echo 'Building result app with docker'
            script{
              docker.withRegistry('https://index.docker.io/v1/','dockerhublogin'){
                def workerImage = docker.build("ksa75/result:v${env.BUILD_ID}","./result")
                workerImage.push()
                workerImage.push("${env.BRANCH_NAME}")

              }
            }
         }
      }
   
      stage('vote build') {
         agent {
             docker {
                 image 'python:2.7.16-slim'
                 args '-u root --env http_proxy="http://172.17.7.137:3129/" --env  https_proxy="http://172.17.7.137:3129/"'
               }
         }
         when{
           changeset "**/vote/**"
         }
         steps {
            echo 'Compiling vote app'
            dir('vote'){
              //sh 'python -m pip install --upgrade pip'
              sh 'pip install -r requirements.txt'
            }
         }
      }
      
      stage('vote test') {
         agent {
             docker {
                 image 'python:2.7.16-slim'
                 args '-u root --env http_proxy="http://172.17.7.137:3129/" --env  https_proxy="http://172.17.7.137:3129/"'
               }
         }
         when{
          changeset "**/vote/**"
         }
         steps {
            echo 'Running Unit Tests on vote app'
            dir('vote'){
              sh 'pip install -r requirements.txt'
              sh 'nosetests -v'
            }            
         }
      }

      stage('vote integration test') {
         agent any
         when{
          changeset "**/vote/**"
          branch 'master'
         }
         steps {
            echo 'Running Integration Tests on vote app'
            dir('vote'){
              sh 'ls -ltr'
              sh './integration_test.sh'
            }
         }
      }

      stage('vote-docker-package') {
         agent any
         when{
           changeset "**/vote/**"
           branch 'master'
         }
         steps {
            echo 'Building vote app with docker'
            script{
              docker.withRegistry('https://index.docker.io/v1/','dockerhublogin'){
                def workerImage = docker.build("ksa75/vote:v${env.BUILD_ID}","./vote")
                workerImage.push()
                workerImage.push("${env.BRANCH_NAME}")

              }
            }
         }
      }

      stage('Sonarqube') {
          when {
               branch 'master'
          }
          agent any
          environment{
            sonarpath = tool 'SonarScanner'
          }
          steps {
              echo 'Running Sonarqube Analysis..'
                withSonarQubeEnv('sonar') {
                  sh "${sonarpath}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
                }
          }
      }      

      stage('deploy to dev') {
         agent any
         when{
           branch 'master'
         }
         steps {
            echo 'Building vote app with docker'
            sh 'docker-compose up -d'
         }
      }

   }

   post{
       always{
           echo 'Build pipeline for instavote app is completed'
       }
       failure{
         slackSend (channel: "instavote-cd", message: "Build Failed - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>))")
       }
       success{
         slackSend (channel: "instavote-cd", message: "Build Succeded - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>))")
           
       }
   }
}
