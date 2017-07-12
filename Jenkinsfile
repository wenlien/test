pipeline {
    agent any
    environment {
        MY_HOME = '~/My_HOME'
        PATH = '/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin' 
        MY_STRING = 'hello world!'
        EMAIL_TO = 'wenlien1001@gmail.com'
        PASS_MESSAGE = 'Yay, we passed.'
        FAIL_MESSAGE = 'Boo, we failed.'
    }
    stages {
        stage('check environment') {
            steps {
                echo "${MY_STRING}"
                echo "${MY_HOME}"
                echo env.PATH
                sh 'env'
                sh 'python --version'
                sh 'git --version'
            }
        }
        // stage('pull code') {
        //    steps {
        //        git url: 'git@github.com:wenlien/test.git'
        //    }
        // }
        stage('retry-timeout') {
            steps {
                timeout(time: 3, unit: 'SECONDS') {
                    retry (3) {
                        sh 'sleep 1'
                    }
                }
            }    
        }
        stage('try-parallel') {
            steps {
                parallel (
                    linux: {
                        echo 'linux'
                        sh 'sleep 1'
                    },
                    mac: {
                        echo 'mac'
                        sh 'sleep 10'
                    },
                    windows: {
                        echo 'windows'
                        sh 'sleep 5'
                    },
                    failFast: false
                )
            }
        }
        stage('get result from shell') {
            steps {
                echo CUR_DATE=$(date +%Y-%m-%d) > env.properties
                echo env.CUR_DATE
            }
        }
        stage('end') {
            steps {
                echo 'end!'
            }
        }
    }
    post {
        always() {
            deleteDir()
        }
        success {
            // mail to: env.EMAIL_TO, subject: "SUCCESS: ${currentBuild.fullDisplayName}", body: env.PASS_MESSAGE
            echo env.PASS_MESSAGE
        }
        failure {
            // mail to: env.EMAIL_TO, subject: "FAILURE: ${currentBuild.fullDisplayName}", body: env.FAIL_MESSAGE
            echo env.FAIL_MESSAGE
        }
    }
}
