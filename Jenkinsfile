@Library("github.com/pwolfbees/pipeline") _

pipeline{
   agent docker:'pwolf/cjptower'

   stages {
      stage('Build and Package'){
         sh "mvn clean package"
         checkpoint "Commit and Package"
      }
      stage ('Publish to S3'){
         step([$class: 'S3BucketPublisher', dontWaitForConcurrentBuildCompletion: false, 
            entries: [[bucket: 'cjptower', excludedFile: '', flatten: false, gzipFiles: false, keepForever: false, 
            managedArtifacts: false, noUploadOnFailure: true, selectedRegion: 'us-west-2', showDirectlyInBrowser: false, 
            sourceFile: 'gameoflife-web/target/gameoflife.war', storageClass: 'STANDARD', uploadFromSlave: true, useServerSideEncryption: false]], 
            profileName: 'cjp-tower-demo', userMetadata: []])
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
