pipeline {
    agent {
        label 'docker'
    } 
    environment {
        ANDROID_HOME = tool name: 'androidSdk'
        http_proxy = "http://192.168.130.250:8080"
        https_proxy = "http://192.168.130.250:8080"
        no_proxy = "localhost,multicert.inet,multicert.dev,10.10.201.21,forge.multicert.inet,192.168.133.69,192.168.139.60,3WS-frontend,3WS-frontend-internal,3WS,cabus,rabus,pkibo,ssl_ev_oa2,*.multicert.dev,*.pipelines.multicert.dev,192.168.139.63,slave-docker"
        proxy_ip = "192.168.130.250"
        proxy_port= "8080"
    }
     options {
        // Stop the build early in case of compile or test failures
        skipStagesAfterUnstable()
    }
  
    stages
    {
    stage("Compile") {
      steps {
        // Compile the app and its dependencies
        sh "./gradlew compileDebugSources"
        //sh "GRADLE_OPTS=-Dgradle.user.home=/home/jenkins/agent/gradle ./gradlew -Dorg.gradle.daemon=false --no-daemon compileDebugSources --stacktrace"
      }
    }
    stage("Unit test") {
      steps {
        // Compile and run the unit tests for the app and its dependencies
        sh "./gradlew testDebugUnitTest testDebugUnitTest"

        // Analyse the test results and update the build result as appropriate
        // junit "**/TEST-*.xml"
      }
    }
    stage("Build APK") {
      steps {
        // Finish building and packaging the APK
        sh "./gradlew assembleDebug"

        // Archive the APKs so that they can be downloaded from Jenkins
        //archiveArtifacts "**/*.apk" // TO REMOVE
      }
    }
    stage("Static analysis") {
      steps {
        // Run Lint and analyse the results
        sh "./gradlew lintDebug"
        //androidLint pattern: '**/lint-results-*.xml'
      }
    }
    stage("Signing APK"){
      steps{

        sh "./gradlew clean assembleRelease"
          echo "Signing APK"
          withCredentials([certificate(aliasVariable: 'MulticertCertificate', credentialsId: 'certificateTest_P12_ID', keystoreVariable: 'certificate_content', passwordVariable: 'certificate_password')]) {
            sh "keytool -list -v -keystore $certificate_content -storepass $certificate_password"
            sh "keytool -importkeystore -srckeystore $certificate_content -destkeystore apkSigning.keystore -srcstoretype pkcs12 -deststoretype pkcs12 -srcstorepass $certificate_password -deststorepass $certificate_password"
            sh "keytool -changealias -keystore apkSigning.keystore -destalias mctCertificate -alias 821499e3-4a4a-42e9-b802-b8d78ea613a8 -storepass $certificate_password"
            sh "keytool -list -v -keystore apkSigning.keystore -storepass $certificate_password"
            sh "jarsigner -verbose -keystore apkSigning.keystore ./app/build/outputs/apk/release/app-release-unsigned.apk mctcertificate -storepass $certificate_password"
            sh "mv ./app/build/outputs/apk/release/app-release-unsigned.apk ./app/build/outputs/apk/release/app-release.apk"
            sh "jarsigner -verify -verbose -certs ./app/build/outputs/apk/release/app-release.apk"
          }
      }
    }
  }
  post {
    always {
      echo 'Clean up our workspace'
      deleteDir()
    }
  }
}