pipeline {
        agent any
        environment {
            AWS_ACCESS_KEY_ID = credentials('aws-access-secret-id')
            AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
            }
        parameters {
             choice(name: 'Terraform', choices: ['Apply', 'Destroy'], description: 'Apply for tf apply, and Destroy for tf destroy')
        }    

        stages {
            stage('pkr inspect') {
                when { expression { return params.Terraform == 'Apply'} }       
                steps {
                    sh 'packer inspect image.json'
                }
            }
            stage('pkr validate') {
                when { expression { return params.Terraform == 'Apply'} }       
                steps {
                    sh 'packer validate image.json'
                }
            }                
            stage('pkr build') {
                when { expression { return params.Terraform == 'Apply'} }       
                steps {
                    sh 'packer build image.json'
                }
            }
            stage('tf init') {
                when { expression { return params.Terraform == 'Apply'} }              
                steps {
                    sh 'terraform init'	
                }
            }
            stage('tf validate') {
                when { expression { return params.Terraform == 'Apply'} }              
                steps {
                    sh 'terraform validate'		
                }
            }
            stage('tf lint') {
                when { expression { return params.Terraform == 'Apply'} }              
                steps {
                    sh 'sudo docker container run -t --rm -v $(pwd):/data wata727/tflint'		
                }
            }
            stage('tf sec') {
                when { expression { return params.Terraform == 'Apply'} }              
                steps {
                    sh 'sudo docker container run -t --rm -v "$(pwd):/src" liamg/tfsec /src'
                }
            }
            stage('tf checkov') {
                when { expression { return params.Terraform == 'Apply'} }              
                steps {
                    sh 'echo "sudo docker container run -t --rm -v "$(pwd):/tf" bridgecrew/checkov -d /tf"'
                }
            }                 
            stage('tf plan') {
                when { expression { return params.Terraform == 'Apply'} }              
                steps {
                    sh 'terraform plan'		
                }
            }               
            stage('tf apply') {
                when { expression { return params.Terraform == 'Apply'} }                       
                steps {
                    input 'Are you sure, you want to Apply this plan?'    
                    sh 'terraform apply --auto-approve'
                }                    
            }                
            stage('tf destroy') {
                when { expression { return params.Terraform == 'Destroy'} }                 
                steps {
                    input 'Are you sure, you want to Destroy this plan?'                      
                    sh 'terraform destroy --auto-approve'
                }                   
            }				
        }
    }
