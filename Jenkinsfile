pipeline {
    agent any 

    environment {
        ANSIBLE_REPO = '/var/lib/jenkins/workspace/ansible_master'
        WEBHOOK = credentials('JENKINS_DISCORD')
    }

    //triggering periodically so the code is always present
    // run every friday at 3AM
    triggers { cron('0 3 * * 5') }

    stages {

        // deploy code to sevastopol, when the branch is 'master'
        stage('deploy') {
            when { branch 'master' }
            steps {
                echo 'deploy and decrypt repo'
                sh 'ansible-playbook ${ANSIBLE_REPO}/deploy/git-crypt.yml -e repo="syncthing" -l "seedbox2"'
            }
        }

        // deploy code to sevastopol, when the branch is 'master'
        stage('restart') {
            when { branch 'master' }
            steps {
                echo 'restart the stack'
                sh 'ansible-playbook ${ANSIBLE_REPO}/deploy/docker/deploy_seedbox2.yml'
            }
        }
    }
    
    post {
        always {
            discordSend \
                description: "${JOB_NAME} - build #${BUILD_NUMBER}", \
                // footer: "Footer Text", \
                // link: env.BUILD_URL, \
                result: currentBuild.currentResult, \
                // title: JOB_NAME, \
                webhookURL: "${WEBHOOK}"
        }
    }
}

