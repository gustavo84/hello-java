// Based on:
// https://raw.githubusercontent.com/redhat-cop/container-pipelines/master/basic-spring-boot/Jenkinsfile

library identifier: "pipeline-library@v1.5",
retriever: modernSCM(
  [
    $class: "GitSCMSource",
    remote: "https://github.com/redhat-cop/pipeline-library.git"
  ]
)

// The name you want to give your Spring Boot application
// Each resource related to your app will be given this name
appName = "hello-java"

pipeline {
    // Use the 'maven' Jenkins agent image which is provided with OpenShift 
    agent { label "maven" }
    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }
   stage('Build') {
        sh "mvn -v"
        sh "mvn clean package -f hello-world/pom.xml"
         
        def jarFile = sh(returnStdout: true, script: 'find hello-world/target -maxdepth 1 -regextype posix-extended -regex ".+\\.(jar|war)\$" | head -n 1').trim()
        sh "cp ${jarFile} app.jar"
    }
    stage('Deploy') {
        sh "oc new-build --name hello-world --binary -n my-project --image-stream=my-project/openjdk-11-rhel7  || true"
        sh "oc start-build hello-world --from-file=app.jar -n my-project --follow --wait"
        sh "oc new-app hello-world || true"
        sh "oc expose svc/hello-world || true"
    }

        // You could extend the pipeline by tagging the image,
        // or deploying it to a production environment, etc......
    }
}
