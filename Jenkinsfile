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
                
        stage('Stage 1') {
            steps {
                echo 'Hello world!'
                    echo "${env.Dummy}"
                    bat 'aws --version'
                //AWS("sts get-caller-identity")
                    //echo "${env.AWS_ACCESS_KEY_ID} and ${env.AWS_SECRET_ACCESS_KEY}" 
            }
        }
                
}
        
}
