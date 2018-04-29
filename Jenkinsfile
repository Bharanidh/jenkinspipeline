pipeline {
  agent none	//don't block an executor for approval
  //see http://bit.ly/2qrz2Ty
  // environment, options, tools, parameters
  //and triggers can also be defined here for the whole pipeline
  triggers { pollSCM('H/5 * * * *') } // poll every 5 mins
  options {
  		timeout(time: 60, unit: 'DAYS')
		buildDiscarder(logRotator(numToKeepStr: '30'))
  }
  stages {
    stage('Build & unit tests') {
      agent any
//      tools { 
	//this is ignored at top level if agent none is specified.
      	//jdk 'Oracle Java 8' (defined in jenkins setup)
//      }	    
      steps {
        echo 'running build and unit tests'
        deleteDir() //delete everything in this workspace
        checkout scm
        //sh './gradlew build'
        //archiveArtifacts
        //stash
      }
//    post {
//     always{
      	//publish unit tests
//      //junit 'path/to/tests/*.xml'
//     }
//    }
    }
    stage('Automated Acceptance tests'){
    	parallel{ //since declarative pipelines 1.2
	    stage('Automated Acceptance tests Firefox'){
	    	agent any
		steps{
			// unstash
			echo "testing Firefox"
		}
		//publish unit tests (omitted here)
	    }
	    stage('Automated Acceptance tests chrome'){
	    	agent any
		steps{
			// unstash
			echo "testing chrome"
		}
		//publish unit tests (omitted here)		    
	    }	    
    	}
    }	    
    stage('Deploy to Stage for User acceptance tests') {
      when { branch 'master' } //only offer this option on master
      steps {
        milestone(1)        
        timeout(time: 30, unit: 'DAYS') {
          input message: 'Deploy to Stage?', submitter: 'admin,tom.tester,pete.pm'
        }
        milestone(2)        
      }
    }
    stage('Deploy to Stage') {
      when { branch 'master' } //only do this on master
      agent any
      steps {
        //unstash                
        //you could tag your code here
        echo 'deploying to stage; running smoke tests'
        script { //switching to the more powerful scripted DSL
          //for script blocks of non-trivial size and/or complexity use Shared Libraries
          def hosts = ['host1', 'host2']
          for (int i = 0; i < hosts.size(); ++i) {
            echo "Deploying to ${hosts[i]}"
          }        
        }   
      }
    }
    stage('Deploy LIVE?') {
      when { branch 'master' } //only do this on master      
      steps {
        milestone(3)     
        timeout(time: 30, unit: 'DAYS') {
          input message:'Deploy to Live?', submitter:'admin'
        }
        milestone(4)
      }
    }    
    stage('Release') {
      when { branch 'master' } //only do this on master      
      agent any      
      steps {
        //unstash        
        echo 'deploying to live and running smoke tests'
      }
    }
  }
  post {
  //feedback on failure (also always, success, unstable, changed, fixed, regression available)
   failure {
    emailext (
     subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
     body: """<p>FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
     	<p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>
	${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
     recipientProviders: [[$class: 'CulpritsRecipientProvider']]
     )
   }
  }
}
