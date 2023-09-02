pipeline {

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    } 
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }

   agent  any
    stages {
        stage('checkout') {
            steps {
                 script{
                        dir("terraform")
                        {
                if (params.awsService == 'EC2') {
                    git "https://github.com/Hariprasadchellamuthu/Terraform1.git"
                } else if (params.awsService == 'S3') {
                    git "https://github.com/Hariprasadchellamuthu/Terraform2.git"
                } else {
                    error("Invalid AWS service selection")
                }                        }
                    }
                }
            }

        stage('Plan') {
            steps {
                sh "cd terraform/${params.awsService.toLowerCase()}; terraform init"
                sh "cd terraform/${params.awsService.toLowerCase()}; terraform plan -out tfplan"
                sh "cd terraform/${params.awsService.toLowerCase()}; terraform show -no-color tfplan > tfplan.txt"
            }
        }
        stage('Approval') {
           when {
               not {
                   equals expected: true, actual: params.autoApprove
               }
           }

           steps {
               script {
                    def plan = readFile 'terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
               }
           }
       }

        stage('Apply') {
            steps {
                sh "cd terraform/${params.awsService.toLowerCase()}; terraform apply -input=false tfplan"
            }
        }
    }

  }
