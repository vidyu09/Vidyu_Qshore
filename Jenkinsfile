pipeline {
  agent {
    node {
      label 'dev'
    }
    
  }
  stages {
    stage('checkout') {
      steps {
        sh '''#!/bin/bash
roscore &'''
      }
    }
    stage('lint') {
      steps {
        sh '''#!/bin/bash
cpplint titan/catkin_ws/src/*/*/*.cpp
cpplint titan/hal/*.*
cpplint titan/hal/c-periphery/*.*

pylint apollo/src/*.py

cpplint sonus/rorbovoice/*.cpp'''
      }
    }
    stage('build') {
      steps {
        sh '''#!/bin/bash
source /opt/ros/kinetic/setup.bash
source titan/catkin_ws/devel/setup.bash
rosrun decisionmanagement decisionmanagement_node &
export GOOGLE_APPLICATION_CREDENTIALS=/home/vishnun/googleapis/gens
export GOOGLE_APPLICATION_CREDENTIALS=/home/vishnun/voice_credentials.json
g++ -o sonus/robovoice/ruta_speechrecognition sonus/robovoice/ruta_speechrecognition.cpp
cd apollo
chmod +x demo_annotate_train.sh
./demo_annotate_train.sh
chmod +x demo_annotateVideo_train.sh
./demo_annotateVideo_train.sh
chmod +x test_demo_annotate_train.sh
./test_demo_annotate_train.sh
chmod +x test_update_ownership_demo_annotate_train.sh
./test_update_ownership_demo_annotate_train.sh
chmod +x test_demo_annotateVideo_train.sh
./test_demo_annotateVideo_train.sh
chmod +x test_update_ownership_demo_annotateVideo_train.sh
./test_update_ownership_demo_annotateVideo_train.sh'''
      }
    }
    stage('test') {
      steps {
        sh '''#!/bin/bash
source titan/catkin_ws/devel/setup.bash
behave -f json.pretty -o titan/test/features/TitanReport.json titan/test/features
python -m behave2cucumber -i titan/test/features/TitanReport.json -o titan/test/features/TitanCucumberReport.json

cd sonus/test/features
export GOOGLEAPIS_GENS_PATH=/home/vishnun/googleapis/gens
export GOOGLE_APPLICATION_CREDENTIALS=/home/vishnun/voice_credentials.json
behave -f json.pretty -o SonusReport.json
python -m behave2cucumber -i SonusReport.json -o SonusCucumberReport.json
cd ..
cd ..
cd ..

cd apollo/roboface-test/features
behave -f json.pretty -o ApolloImgReport.json
python -m behave2cucumber -i ApolloImgReport.json -o ApolloImgCucumberReport.json
cd ..
cd ..
cd ..

cd apollo/videotest/features
behave -f json.pretty -o ApolloVidReport.json
python -m behave2cucumber -i ApolloVidReport.json -o ApolloVidCucumberReport.json'''
        step([$class: 'CucumberReportPublisher', jsonReportDirectory: '', fileIncludePattern: '**/*CucumberReport.json',ignoreFailedTests: false])
        node('master'){
        
          publishHTML (target: [
      allowMissing: false,
      alwaysLinkToLastBuild: false,
      keepAll: true,
      reportDir: '/var/lib/jenkins/jobs/robosystems/branches/october-2017-release-1-0.oko3qd/builds/${BUILD_NUMBER}/cucumber-html-reports',
      reportFiles: 'overview-features.html',
      reportName: "October Release 1.0"
    ])
        }
        script {
            def testIssue = [fields: [ project: [id: '10003'],
                           summary: 'Titan-18_DecisionManagment-Entrance-check-is-failing',
                           description: 'Titan-18_DecisionManagment-Entrance-check-is-failing.',
                           issuetype: [id: '10004']]]

            //response = jiraNewIssue issue: testIssue, site: 'JIRA'
            //jiraLinkIssues inwardKey: response.data.key, outwardKey: 'TITAN-18', site: 'JIRA', type: 'Blocks'

        } 
      }
    }
    stage('qa-deploy'){
      steps{
          archiveArtifacts artifacts: '**', caseSensitive: false, defaultExcludes: false, onlyIfSuccessful: true
          node('master'){
            sh'''#!/bin/bash
            sshpass -p "jenkins" rsync -avr -e "ssh -l user" /var/lib/jenkins/jobs/robosystems/branches/october-2017-release-1-0.oko3qd/builds/${BUILD_NUMBER}/archive jenkins@35.202.36.226:/home/jenkins
            '''
          }
          
      }
    }
    stage('qa-test') {
      steps {
      node('apollo-test'){
        sh '''#!/bin/bash
        cd /home/jenkins/archive
source titan/catkin_ws/devel/setup.bash
behave -f json.pretty -o titan/test/features/TitanReport.json titan/test/features
python -m behave2cucumber -i titan/test/features/TitanReport.json -o titan/test/features/TitanCucumberReport.json

cd sonus/test/features
export GOOGLEAPIS_GENS_PATH=/home/vishnun/googleapis/gens
export GOOGLE_APPLICATION_CREDENTIALS=/home/vishnun/voice_credentials.json
behave -f json.pretty -o SonusReport.json
python -m behave2cucumber -i SonusReport.json -o SonusCucumberReport.json
cd ..
cd ..
cd ..

cd apollo/roboface-test/features
behave -f json.pretty -o ApolloImgReport.json
python -m behave2cucumber -i ApolloImgReport.json -o ApolloImgCucumberReport.json
cd ..
cd ..
cd ..

cd apollo/videotest/features
behave -f json.pretty -o ApolloVidReport.json
python -m behave2cucumber -i ApolloVidReport.json -o ApolloVidCucumberReport.json'''
        step([$class: 'CucumberReportPublisher', jsonReportDirectory: '', fileIncludePattern: '**/*CucumberReport.json',ignoreFailedTests: false])
        node('master'){
        
          publishHTML (target: [
      allowMissing: false,
      alwaysLinkToLastBuild: false,
      keepAll: true,
      reportDir: '/var/lib/jenkins/jobs/robosystems/branches/october-2017-release-1-0.oko3qd/builds/${BUILD_NUMBER}/cucumber-html-reports',
      reportFiles: 'overview-features.html',
      reportName: "QA October Release 1.0"
    ])
        }
        script {
            def testIssue = [fields: [ project: [id: '10003'],
                           summary: 'Titan-18_DecisionManagment-Entrance-check-is-failing',
                           description: 'Titan-18_DecisionManagment-Entrance-check-is-failing.',
                           issuetype: [id: '10004']]]

            //response = jiraNewIssue issue: testIssue, site: 'JIRA'
            //jiraLinkIssues inwardKey: response.data.key, outwardKey: 'TITAN-18', site: 'JIRA', type: 'Blocks'

        } 
        }
      }
    }
  }
  
  environment {
    test = 'dev'
  }
}
