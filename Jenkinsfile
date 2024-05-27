pipeline {
    agent any

    environment {
        EMAIL_TO = "thanosronaldo7@gmail.com"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:ThanosZappas/family-doctor-ansible.git'
            }
        }
        // stage('Test') {
        //     steps {
        //         sh './mvnw test'
        //     }
        // }
        
        //install ansible on jenkinsvm
        stage('run ansible pipeline') {
            steps {
                build job: 'ansible'
            }
        }
        stage('install ansible prerequisites') {
            steps {
                sh '''
                    ansible-galaxy install geerlingguy.postgresql
                '''
            }
        }
        stage('Install postgres') {
            steps {
                sh '''
                    export ANSIBLE_CONFIG=/etc/ansible/ansible.cfg
                    ansible-playbook -i ~/workspace/ansible-test/playbooks/hosts.yaml -l azure-db-server ~/workspace/ansible-test/playbooks/postgres.yaml
                '''
            }
        }

        stage('Deploy spring boot app') {
            steps {
                sh '''
                   # replace dbserver in host_vars
                     sed -i 's/dbserver/4.211.130.185/g' ~/workspace/ansible-test/host_vars/appserver-vm.yaml
                   # replace workingdir in host_vars
                     sed -i 's/vagrant/azureuser/g' ~/workspace/ansible-test/host_vars/appserver-vm.yaml
                '''
                sh '''
                    # edit host var for appserver

                    export ANSIBLE_CONFIG=~/workspace/ansible-test/ansible.cfg
                    ansible-playbook -i ~/workspace/ansible-test/hosts.yaml -l appserver-vm ~/workspace/ansible-test/playbooks/spring.yaml
                '''
            }
        }
       // stage('Deploy frontend') {
       //      steps {
       //          sh '''
       //              sed -i 's/dbserver/4.211.130.185/g' ~/DevOps/family-doctor-ansible/host_vars/appserver-vm.yaml
       //              export ANSIBLE_CONFIG=~/DevOps/family-doctor-ansible//ansible.cfg
       //              ansible-playbook -i ~/workspace/ansible/hosts.yaml -l appserver-vm -e branch=main -e backend_server_url=http://localhost:9090 ~//DevOps/family-doctor-ansible/playbooks/vuejs.yaml
       //          '''
       //      }
       // }
    }

    post {
        always {
            mail  to: "${EMAIL_TO}", body: "Project ${env.JOB_NAME} <br>, Build status ${currentBuild.currentResult} <br> Build Number: ${env.BUILD_NUMBER} <br> Build URL: ${env.BUILD_URL}", subject: "JENKINS: Project name -> ${env.JOB_NAME}, Build -> ${currentBuild.currentResult}"
        }
    }
}
