pipeline {

  agent any

  parameters {
    choice(
      name: 'ENV',
      choices: ['dev', 'prod'],
      description: 'Select the environment'
    )
    choice(
      name: 'ACTION',
      choices: ['plan', 'apply', 'destroy'],
      description: 'Terraform action'
    )
  }

  environment {
    TF_IN_AUTOMATION = 'true'
  }

  stages {

    stage('Checkout GitHub Repo') {
      steps {
        git url: 'https://github.com/rutwik30usr/sat-deployment.git'
      }
    }

    stage('Terraform Init') {
      steps {
        dir('terraform') {
          sh 'terraform init'
        }
      }
    }

    stage('Terraform Action') {
      steps {
        withCredentials([
          string(credentialsId: 'AWS_ACCESS_KEY', variable: 'AWS_ACCESS_KEY_ID'),
          string(credentialsId: 'AWS_SECRET_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
        ]) {
          dir('terraform') {
            script {
              if (params.ACTION == 'plan') {
                sh "terraform plan -var-file=${params.ENV}.tfvars"
              }

              if (params.ACTION == 'apply') {
                sh "terraform apply -auto-approve -var-file=${params.ENV}.tfvars"
              }

              if (params.ACTION == 'destroy') {
                sh "terraform destroy -auto-approve -var-file=${params.ENV}.tfvars"
              }
            }
          }
        }
      }
    }
  }

  post {
    always {
      echo "Finished: ${params.ACTION} on environment ${params.ENV}"
    }
  }
}
