pipeline {
    agent any

 stages {
      stage('checkout') {
           steps {
                git credentialsId: 'github-creds', url: 'https://github.com/kishanth94/ansible-pipeline-testing.git' 
          }
        }
		
	  stage("Copying the playbook to Ansible server"){
		    steps{
			    script{
					    sh '''
                        echo ${WORKSPACE}
						scp -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/id_rsa ${WORKSPACE}/playbooks/apachewebserver.yml ansadmin@172.31.16.191:/opt/ansible/playbooks/apachewebserver.yml
                        scp -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/id_rsa ${WORKSPACE}/inventory/prod_hosts ansadmin@172.31.16.191:/opt/inventory/prod_hosts
						'''     
				}
            }
        }
	
	 stage('Applying changes using playbook') {
            steps {
               script{
			        sh 'ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/id_rsa ansadmin@172.31.16.191 "ansible-playbook -i /opt/inventory/prod_hosts /opt/ansible/playbooks/apachewebserver.yml; sudo rm -rf /opt/ansible/playbooks/apachewebserver.yml;"'   
               }
            }
        }
    }
	
	post {
	  always {
	    echo 'Deleting the Workspace'
	    deleteDir() /* Clean Up our Workspace */
	  }
	    success {
		  mail to: 'devopsawsfreetier@gmail.com',
		  subject: "Success Build Pipeline: ${currentBuild.fullDisplayName}",
		  body: "The pipeline ${env.BUILD_URL} completed successfully"
	    }
	    failure {
  	      mail to: 'devopsawsfreetier@gmail.com',
 		  subject: "Failed Build Pipeline: ${currentBuild.fullDisplayName}",
 		  body: "Something is wrong with ${env.BUILD_URL}"
 	    }
    }
}