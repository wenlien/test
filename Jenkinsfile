#!/usr/bin/env groovy
import groovy.json.JsonSlurper;


node {
        stage('Checking Environment') {
            env.PATH='/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin'
            sh 'python3 --version'
            sh 'git --version'
            sh 'php --version'
        }
        state('test') {
            env.PATH='/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin'
            sh 'env'
            sh 'python3 --version'
        }
        stage('Init Environment') {
            juvo_config_replacement()
            script {
                recipient = get_email()
            }
        }
        stage('Run Regression Testing') {
            env.PATH='/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin'
            try {
                parallel (
                        "Gating": {
                            node('Gating') {
                                // sh 'php ./DailyReport.php -m -p -e -l 3000 -b'
                                sh 'echo Gating'
                            }
                        },
                        "Gating-Candidate": {
                            node('Gating-Candidate') {
                                sh 'echo Gating-Candidate'
                            }
                        },
                        "Non-Gating": {
                            node('Non-Gating') {
                                sh 'echo Non-Gating'
                            }
                        }
                )
            }
            catch(error) {
                throw error
            }
            finally {
                TESTING_SUCCESSED = True
            }
        }
        stage('Deployment') {
            if (TESTING_SUCCESSED) {
                sh 'echo Deployment'
            }
        }
        stage('Email') {
            if (TESTING_SUCCESSED) {
                sh 'echo Successed!'
                mail subject: "${env.JOB_NAME} (${env.BUILD_NUMBER}) successed",
                    body: "It appears that ${env.BUILD_URL} is failing, somebody should do something about that",
                    to: recipient,
                    replyTo: recipient,
                    from: 'noreply@gmail.com'
            }
            else {
                sh 'echo Failure!'
                mail subject: "${env.JOB_NAME} (${env.BUILD_NUMBER}) failure",
                    body: "It appears that ${env.BUILD_URL} is failing, somebody should do something about that",
                    to: recipient,
                    replyTo: recipient,
                    from: 'noreply@gmail.com'
            }
        }
    //}
}


def juvo_config_replacement() {
    env.PATH='/usr/local/bin:/usr/local/sbin:/usr/bin:/usr/sbin:/bin:/sbin'
    String juvo_config_path = '~/.juvoconfig';
    juvo_config_path = env.HOME + juvo_config_path.substring(1);
    sh 'cp ' + juvo_config_path + '.orig ' + juvo_config_path
    sh 'python -c \'import json; j=json.load(open("' + juvo_config_path +'", "r")); j["db"]["host"]="10.0.0.156"; json.dump(j, open("' + juvo_config_path + '", "w"), indent=True)\';'
}

def get_email() {
    script {
        email = sh (
            script: "git log | grep Author | head -1 | cut -d'<' -f2 | cut -d'>' -f1",
            returnStdout: true
            )
    }
    return email
}
