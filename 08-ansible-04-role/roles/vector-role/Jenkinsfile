pipeline {
    agent {
        label 'linux'
    }
    stages {
        stage('Git') {
            steps{
                git branch: 'main', credentialsId: '7943d9ab-efa7-4520-b128-48f13295c4ae', url: 'https://github.com/Valdem88/dev-17_ansible-04-role-yakovlev_vs.git'
            }
        }
        stage('Molecule install') {
            steps{
                sh 'pip3 install molecule==3.4.0'
                sh 'pip3 install ansible-lint==5.1.3'
                sh 'pip3 install molecule_docker'
            }
        }
        stage('Molecule test'){
            steps{
                dir('roles/vector-role/') {
                    sh 'ls -l'
                    sh 'molecule test'
                    cleanWs()
                }
            }
        }
    }
}
