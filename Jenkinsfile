#!/usr/bin/env groovy
import groovy.json.JsonSlurper;


node {
    stage('Checking Environment') {
        sh 'python3 --version'
        sh 'git --version'
        sh 'php --version'
    }
    stage('Init Environment') {
        juvo_config_replacement()
    }
    stage('Run Regression Testing') {
        try {
            parallel Gating: {
                // sh 'php ./DailyReport.php -m -p -e -l 3000 -b'
                sh 'echo Gating'
            },
            Gating-Candidate: {
                sh 'echo Gating-Candidate'
            },
            Non-Gating: {
                sh 'echo Non-Gating'
            }
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
        }
        else {
            sh 'echo Failure!'
        }
    }
}


def juvo_config_replacement() {
    String juvo_config_path = '~/.juvoconfig';
    juvo_config_path = env.HOME + juvo_config_path.substring(1);
    sh 'cp ' + juvo_config_path + '.orig ' + juvo_config_path
    sh 'python -c \'import json; j=json.load(open("' + juvo_config_path +'", "r")); j["db"]["host"]="10.0.0.156"; json.dump(j, open("' + juvo_config_path + '", "w"), indent=True)\';'
}
