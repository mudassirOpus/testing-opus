pipeline{
    
    agent any

    environment{
        globalVar= 'I came from global env.'
        AUTHOR_EMAIL= 'mudassir@parseclabs.ca'
    } 
    tools{
        nodejs '16.14.0'
    } 
    
    parameters{
        string(name: 'AUTHOR_NAME', defaultValue: 'Mudassir', description: 'This is author name')
        booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value')
        choice(name: 'CHOICE', choices: ['one', 'two', 'three'], description: 'You have your own choice')
        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter your password')
    }

    stages{
        stage('Commit Details') {
            steps {
                script {
                    env.CHANGE_COMMIT_MSG = sh (script: 'git log -1 --pretty=%B', returnStdout: true).trim()
                    env.CHANGE_AUTHOR = sh (script: 'git log -1 --pretty=%cn', returnStdout: true).trim()
                    env.CHANGE_AUTHOR_EMAIL = sh (script: 'git log -1 --pretty=%ae', returnStdout: true).trim()
                    env.CHANGE_COMMITOR_EMAIL = sh (script: 'git log -1 --pretty=%ce', returnStdout: true).trim()
                    env.CHANGE_HASH = sh (script: 'git log -1 --pretty=%h', returnStdout: true).trim()
                    echo "Author ${env.CHANGE_AUTHOR}"
                    echo "Author EMAIL: ${env.CHANGE_AUTHOR_EMAIL}"
                    echo "Commitor EMAIL: ${env.CHANGE_COMMITOR_EMAIL}"
                    echo "Commit Message:  ${env.CHANGE_COMMIT_MSG}"
                    echo "Commit Hash:  ${env.CHANGE_HASH}"
                }
            }
        }
        stage('Welcome Test'){
            environment{
                localVar= 'I came from local env.'
                name= 'Mudassir'
            }
            steps{
                echo "1: Hi ${name}, ${globalVar}"
                echo "2: And, ${localVar}"
                echo 'github webhook is also working fine'
            }
        }
        stage("Checking the Parameters"){
            steps{
                echo "Cheking the defined parameters..."
                echo "Hi, my name is ${params.AUTHOR_NAME}"
                echo "Toggle: ${params.TOGGLE}"
                echo "Choice: ${params.CHOICE}"
                echo "Passcode: ${params.PASSWORD}"
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm version'
                sh 'pip install pylint'
                //for production environment - installing wrangler to interact with cloudflare worker
                sh 'npm install -g wrangler'
                sh 'npx wrangler login --config ./wrangler.toml'
                sh 'ls -a'
                sh 'npx wrangler init testing-opus-ai'
                sh 'ls -a'
                sh 'cd testing-opus-ai'
                sh 'ls -a'
                /* sh 'CLOUDFLARE_ACCOUNT_ID=account_id CLOUDFLARE_API_TOKEN=Dzd5Vp8F4GnIIhW1-J3-VvEG_gxVe1pwddHa7qnZ npx wrangler pages publish --config ./wrangler.toml --branch=preview ./public' */
            }
        }
        stage("Release"){
            steps{
                echo 'product is released'
            }
        }
    }

    post{
        always{
            echo "Running post build steps..."
        }
        success{
            echo "Job was successfull..."
            echo "Sending email for job success..."
            
            mail charset: 'UTF-8', from: '', mimeType: 'text/html',
            to: "${AUTHOR_EMAIL}",
            replyTo: '',
            cc: '',
            bcc: '',
            subject: "Build successfull for Commit ${env.CHANGE_HASH} -> ${env.JOB_NAME}",
            body: "<br> Dear ${env.CHANGE_AUTHOR},<br><br> The build ${env.BUILD_NUMBER} for Project: ${env.JOB_NAME} completed successfully!<br><br>" +
            "Please visit the following URL to verify:<br> URL: ${env.BUILD_URL}<br><br>Regards,<br>Jenkins CI";
        }
        failure{
            echo "Job was Failed..."
            echo "Sending email for job failed..."

            mail charset: 'UTF-8', from: '', mimeType: 'text/html',
            to: "${AUTHOR_EMAIL}",
            replyTo: '',
            cc: '',
            bcc: '',
            subject: "Build Failed for Commit ${env.CHANGE_HASH} -> ${env.JOB_NAME}",
            body: "<br> Dear ${env.CHANGE_AUTHOR},<br><br> The build ${env.BUILD_NUMBER} for Project: ${env.JOB_NAME} Failed!<br><br>" +
            "Please visit the following URL to verify:<br> URL: ${env.BUILD_URL}<br><br>Regards,<br>Jenkins CI";
        }
        unstable {  
            echo 'Job is unstable!'
            echo 'Sending email for unstable job!'
            mail charset: 'UTF-8', from: '', mimeType: 'text/html',
            to: "${AUTHOR_EMAIL}",
            cc: '',
            replyTo: '',
            bcc: '',
            subject: "Build is Unstable -> ${env.JOB_NAME}",
            body: "<br> Dear Team,<br><br> The build for Project: ${env.JOB_NAME} is unstable!<br><br>" +
            "Please visit the following URL to review:<br> URL: ${env.BUILD_URL}<br><br>Regards,<br>Jenkins CI";
        }  
        changed {  
            echo 'This will run only if the state of the Pipeline has changed'  
            echo 'For example, if the Pipeline was previously failing but is now successful'  
        }  
    }

}
