pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Deploy To Staging') {
            when{
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
                    //
                    sshPublisher (
                        failOnError: true, 
                        publishers: [sshPublisherDesc(configName: 'staging',
                                                      sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                                      transfers: [sshTransfer(excludes: '', execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule', 
                                                                              execTimeout: 120000, 
                                                                              remoteDirectory: '/tmp', 
                                                                              removePrefix: 'dist/', 
                                                                              sourceFiles: 'dist/trainSchedule.zip')], 
                                                      usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)]
                        )
                    //
                
            }
        }
        stage('Deploy To Production') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
    }
}
