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
        stage('deploy prd code') {
            when { branch 'master' }
            steps {
                // deploy configs to PRD
                echo 'deploy git repo'
                // sh 'ansible-playbook ${ANSIBLE_REPO}/deploy/docker/deploy_docker_compose_prd.yml --extra-vars repo="privateer"'
                echo 'decrypt repo'
                sh 'ansible-playbook ${ANSIBLE_REPO}/deploy/git-crypt.yml -e repo="syncthing" -l "seedbox2"'
            }
        }

        //TODO: redeploy the stack

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

