
stage ('checkout') {
	steps{
		checkout scm
	}
}

stage ('compile') {
	steps{
		script{
		  def buildTasks = [
		     "worscipe-plugin-name": "plugins/worscipe-plugin-name",
		     "worscipe": "themes/worscipe"
		  ]
		  
		  // Run build tassks for every item in the list.
		  build = [:]
		  for (kt in mapToList(buildTasks)) {
		  	  build[ kt[0] ] = runGruntGulp( kt[0], kt[1])
		  }
		  parallel build
		}
	}
}

stage ('deploy') {
	steps{
	  sh 'rm -vfR composer.lock'
	  
	  sh 'git status'
	  
	  script{
	  
	  	switch(env.BRANCH_NAME) {
	  		case "master":
	  			sh 'composer install'
	  			withCredentials([usernamePassword( credentialsId: 'jenkins_cred_id', usernameVariable: '' ]) {
	  			  sh 'vendor/bin/phploy --list'
	  			  sh 'vendor/bin/phploy -s master'
	  			}
	  			break;
	     	// case "staging":
	     	// case "prod" :
	        default:
	        	echo "Skipping depoly, no deploy for branch '${env.BRANCH_NAME}'"
	        	break; 
	  	}
	  
	  }
	}
}