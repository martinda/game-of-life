@Library("github.com/pwolfbees/pipeline") _

pipeline{
   agent docker:'pwolf/cjptower'
   
   notifications {
        success {
            mail to:"devops@example.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Build passed."
        }
        failure {
            mail to:"devops@example.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Build failed."
        }
        unstable {
            mail to:"devops@example.com", subject:"UNSTABLE: ${currentBuild.fullDisplayName}", body: "Build unstable."
        }
        changed {
            mail to:"devops@example.com", subject:"CHANGED: ${currentBuild.fullDisplayName}", body: "Build status changed!"
        }
    }

   stages {
      stage('Build and Package'){
         sh "mvn clean package"
      }
      stage ('Publish Artifact to S3'){
        wrap([$class: 'AmazonAwsCliBuildWrapper', credentialsId: 's3-cjptower', defaultRegion: 'us-west-2']) {
            sh 'aws s3 cp gameoflife-web/target/gameoflife.war s3://cjptower/gameoflife.war'
        }
      }
      stage ('Promote Build'){
        script{
            env.TARGET = input message: 'Deploy Application?', ok: 'Go! Go! Go!', parameters: [choice(choices: 'Development\nStaging\nProduction', description: 'Pick Target Environment for Deployment.', name: 'TARGET')]
        }
      }
      stage("Run Tower Playbook"){
         withTower(host:"https://104.198.10.204", credentials:"tower-cli"){
            sh "tower-cli job launch --job-template=31 --extra-vars='env='${env.ENV}"
         }
      }   
   }
}
