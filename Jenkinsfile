pipeline {
environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        env.AWS_ACCESS_KEY_ID = AWS_ACCESS_KEY_ID
        env.AWS_SECRET_ACCESS_KEY = AWS_SECRET_ACCESS_KEY
    }
        agent  any

stages {
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
                echo 'Password  ${env.AWS_ACCESS_KEY_ID} and ${env.AWS_SECRET_ACCESS_KEY}' 
            }
        }
}
        
}
