@Library("github.com/pwolfbees/pipeline") _

pipeline{
   agent docker:'pwolf/cjptower'
   
   notifications {
        always {
            echo "Job's Done."
        }
        success {
            echo "Success!!!"
        }
        failure {
            echo "I FAILED."
        }
        changed {
            echo "Things were different before..."
        }
    }

   stages {
      stage('Build and Package'){
         sh "mvn clean package"
      }
      stage ('Publish to S3'){
        wrap([$class: 'AmazonAwsCliBuildWrapper', credentialsId: 's3-cjptower', defaultRegion: 'us-west-2']) {
            sh 'aws s3 cp gameoflife-web/target/gameoflife.war s3://cjptower/gameoflife.war'
        }
      }
      stage ('Input'){
        script{
            env.ENV = input message: 'Deploy Application?', ok: 'Go! Go! Go!', parameters: [choice(choices: 'Development\nStaging\nProduction', description: 'Pick Environment to Deploy.', name: 'ENV')]
        }
      }
      stage("Run Playbook"){
         withTower(host:"https://104.198.10.204", credentials:"tower-cli"){
            sh "tower-cli job launch --job-template=31 --extra-vars='env='${env.ENV}"
         }
      }   
   }
}
