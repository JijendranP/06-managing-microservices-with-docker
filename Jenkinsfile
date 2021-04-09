pipeline {
  environment {
    imagename1 = "jijendran/vote"
    imagename2 = "jijendran/result"
    imagename3 = "jijendran/worker"
    registryCredential = 'dockerhub'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        //git([url: ', branch: 'master'])
        sh 'Checkout'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage1 = docker.build("$imagename1", "./vote/")
          dockerImage2 = docker.build("$imagename2", "./result/")
          dockerImage3 = docker.build("$imagename3", "./worker/")
        }
      }
    }
    
    stage('Push Image to Docker Registry') {
      steps{
        script {
          //sh "echo 'Image pushed to Registry'"
          withDockerRegistry(credentialsId: 'docker', url: 'https://index.docker.io/v1/') {
            dockerImage1.push('latest')
            dockerImage2.push('latest')
            dockerImage3.push('latest')
          }
        }
        
      }
    }
    
    stage('Ansible Init') {
      steps {
        script {
          def tfHome = tool name: 'ansible'
          env.PATH = "${tfHome}:${env.PATH}"
          sh 'ansible --version'
          }
        }
    }
        
    stage('Ansible Deploy on Remote Server') {
      steps {
        ansiblePlaybook credentialsId: 'ansible', installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible.yml'
      }
    }
  }    
}  
