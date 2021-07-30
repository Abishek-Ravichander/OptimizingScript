pipeline {
        agent  any        
        stages {
        stage("Env Variables") {
steps {
        script {
        env.AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        env.AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')   
        env.Dummy = "Some thing"
        }
        
    }
        }
        
        stage('checkout') {
            steps {
                 script{

                        
                            git "https://github.com/Abishek-Ravichander/OptimizingScript.git"
                        
                    }
                }
            } 
                
        stage('Sample') {
            steps {
                echo 'Hello world!'
                    echo "${env.Dummy}"
                    sh 'echo  stage1 steps'
                   sh 'aws --version'
                //AWS("sts get-caller-identity")
                    //echo "${env.AWS_ACCESS_KEY_ID} and ${env.AWS_SECRET_ACCESS_KEY}" 
            }
        }
                stage('Pre-build'){
        steps {
            sh '''#!/bin/bash
            rm -f prop.properties
            echo -ne 'CIUUID='>>prop.properties
            uuidgen>>prop.properties
            '''

            script {
                def props = readProperties file: 'prop.properties'
                env.CIUUID = props.CIUUID
            }

            sh "echo The CI UUID is $CIUUID" 
                // Clean up
            sh'''#!/bin/bash
            echo "\n---> whoami"
            whoami
            echo "\n---> List of files"
            ls -a
            echo "\n Removing .terraform files for clean execution"
            find . -name "*.terraform*" -exec rm -rf {} \\;
            find . -name "*.tfstate*" -exec rm -rf {} \\;
            find . -name "*plan.out*" -exec rm -rf {} \\;
            find . -name "*.log*" -exec rm -rf {} \\;
            find . -name "*terraform-security-rules*" -exec rm -rf {} \\;

            echo "\n---> List of files (After cleanup)"
            ls -a
            echo "\n---> Check Root Files" 
            ls / -a
            echo "\n---> Check Version"
            python --version
            terraform --version
            '''
        }
                }
                stage('Set Environment Variables') {        
        steps {
            sh "echo The Branch name is"
                 script {
                def datas = readYaml file: 'deployment.yaml'
                env.REGION = datas.region
                env.ACCOUNTID = datas.accountId
                env.ROLENAME = datas.roleName
                env.ROLE="arn:aws:iam::" + env.ACCOUNTID + ":role/" + env.ROLENAME 
            }

            sh "echo The region is $REGION" 
            sh "echo The Account Id is $ACCOUNTID"
            sh "echo The Role is $ROLE"
        }
                }
                
                stage('Initialization') {
        steps {
            sh'''#!/bin/bash
            export AWS_DEFAULT_REGION="$REGION"
            echo $AWS_DEFAULT_REGION
            export ROLE="$ROLE"
            echo "===== assuming permissions => $ROLE ====="
            account_role=`aws sts assume-role --role-arn $ROLE --role-session-name "jenkins-valkyrie-$CIUUID"`
            export AWS_ACCESS_KEY_ID=$(echo $account_role | credentials('AWS_ACCESS_KEY_ID'))
            export AWS_SECRET_ACCESS_KEY=$(echo $account_role | credentials('AWS_SECRET_ACCESS_KEY'))
            export AWS_SESSION_TOKEN=$(echo $account_role | jq -r .Credentials.SessionToken)
            export AWS_SECURITY_TOKEN=$(echo $account_role | jq -r .Credentials.SessionToken)
            aws sts get-caller-identity

            echo "\nChanging the directory..."
            cd root
            pwd
            echo "\nTerraform Initialization..."
            terraform init
            '''
        }
    }
                
    stage('Validate Plan') {
        steps {
            sh'''#!/bin/bash
            export AWS_DEFAULT_REGION="$REGION"
            echo $AWS_DEFAULT_REGION
            export ROLE="$ROLE"
            echo "===== assuming permissions => $ROLE ====="
            account_role=`aws sts assume-role --role-arn $ROLE --role-session-name "jenkins-valkyrie-$CIUUID"`
            export AWS_ACCESS_KEY_ID=$(echo $account_role | jq -r .Credentials.AccessKeyId)
            export AWS_SECRET_ACCESS_KEY=$(echo $account_role | jq -r .Credentials.SecretAccessKey)
            export AWS_SESSION_TOKEN=$(echo $account_role | jq -r .Credentials.SessionToken)
            export AWS_SECURITY_TOKEN=$(echo $account_role | jq -r .Credentials.SessionToken)
            
            echo "\nChanging the directory..."
            cd root
            pwd
            set -e
            echo "\nTerraform Format..."
            terraform fmt -recursive
            echo "\nTerraform Validate..."
            terraform validate
            echo "\nTerraform Plan..."
            terraform plan -out=plan.out
            echo "\n----> Resources will be deployed to $ACCOUNTID\n"
            '''
            // tf compliance --> requires terraform plan output file
        }
    }             
                
}
        
}
