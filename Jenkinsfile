
properties([  parameters([
  credentials (credentialType: 'com.cloudbees.jenkins.plugins.awscredentials.AWSCredentialsImpl', defaultValue: '', description: '', name: 'CREDENTIALS', required: false), choice (choices: ['us-east-1', 'us-west-2 ', 'eu-west-1'], description: 'Please Choose your Region', name: 'REGION'),
  string (defaultValue: '', description: 'Please type the new Plan Name', name: 'Plan_Name', trim: false),
  string (defaultValue: '', description: 'Please type the new Vault Name', name: 'VAULT_NAME', trim: false),
  string (defaultValue: '', description: 'Please type backup Rule Name', name: 'Rule_Name', trim: false),
  string (defaultValue: '', description: 'Please type Role Name', name: 'IAM_Role', trim: false),
  string (defaultValue: '', description: 'Please type correct Tag name of EC2/RDS', name: 'selection_tag', trim: false),
  string (defaultValue: '', description: 'Please type Resource Name', name: 'Resource_Name', trim: false),
  string (defaultValue: '', description: 'Please type Backup URL', name: 'Backup_URL', trim: false),
  string (defaultValue: '', description: 'Please type SNS ARN', name: 'SNS_ARN', trim: false),
  booleanParam(name: 'APPLY_CHANGES', defaultValue: false, description: 'If not opted, it will be dry run')
 
  
  
   ])
])
pipeline {
    agent any

  stages {
        stage('git checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/jitendrapsingh/jenkinsfile2backup'
            }
        }
        stage('terrafrom  init'){
            steps{
                sh '''
		set +x
                PATH=/usr/local/bin
                terraform init'''
            }
        }
		 stage('Setup Env') {
            steps {
    		  script {
			      currentBuild.displayName = "#${params.APPLY_CHANGES ? 'Apply (Create Resources)' : 'Plan (Dry Run)' }"
				  currentBuild.description = "${params.APPLY_CHANGES ? 'Apply (Create Resources)' : 'Plan (Dry run)' }"
			  
                }
           }
		  }
        
        stage('terraform plan') {
            steps {
    		  withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', 
			  accessKeyVariable: 'ACCESS_KEY', 
			  credentialsId: CREDENTIALS, 
			  secretKeyVariable: 'SECRET_KEY']]) {
			  if (params.APPLY_CHANGES == 'true') {
			  sh '''
			  then
			  set +x
			  PATH=/usr/local/bin
			  terraform apply --auto-approve -var "REGION=$REGION" -var "ACCESS_KEY=$ACCESS_KEY" -var "SECRET_KEY=$SECRET_KEY" -var "Plan_Name=$Plan_Name" -var "VAULT_NAME=$VAULT_NAME" -var "Rule_Name=$Rule_Name" -var "IAM_Role=$IAM_Role" -var "selection_tag=$selection_tag" -var "Resource_Name=$Resource_Name"
             }else{
			     terraform plan   -var "REGION=$REGION" -var "ACCESS_KEY=$ACCESS_KEY" -var "SECRET_KEY=$SECRET_KEY" -var "Plan_Name=$Plan_Name" -var "VAULT_NAME=$VAULT_NAME" -var "Rule_Name=$Rule_Name" -var "IAM_Role=$IAM_Role" -var "selection_tag=$selection_tag" -var "Resource_Name=$Resource_Name"'''
			 }
           }
         }
		 }  
      
	 
  }
		  
	post { 
        always { 
            cleanWs()
        
    }
 }
}
