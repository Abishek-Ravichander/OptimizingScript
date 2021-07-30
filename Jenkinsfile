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
                
                 
                
}
        
}
