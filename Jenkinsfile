pipeline {
    agent any
    environment {
      ANSIBLE_PRIVATE_KEY=credentials('ssh-key')
    }
    triggers {
       pollSCM '* * * * *'
    }
    stages {
        stage('Build and Push') {
            steps {
                 withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'pass', usernameVariable: 'user')]) {
                   sh "docker build -t mikatrooper/holyexam:v$BUILD_ID ."
                   sh "docker login -u $user -p $pass"
                   sh "docker push mikatrooper/holyexam:v$BUILD_ID"
                }
            }
        }
        stage('Deploy') {
            steps {
                 withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'pass', usernameVariable: 'user')]) {
                   sh "ansible-playbook playbook.yaml -i inventory --private-key=ANSIBLE_PRIVATE_KEY -u ansible --become -e username=$user -e password=$pass -e BUILD_ID=$BUILD_ID"
                }
            }
        }
    }
}

