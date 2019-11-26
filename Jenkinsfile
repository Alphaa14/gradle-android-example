pipeline {
    agent {
        label 'docker'
    } 
    environment {
        ANDROID_HOME = tool name: 'androidSdk'
        GLIBC = tool name: "alpine-pkg-glibc"
    }
    tools {
       gradle "gradle562"
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
        sh "GRADLE_OPTS=-Dgradle.user.home=/home/jenkins/agent/gradle ./gradlew -Dorg.gradle.daemon=false --no-daemon compileDebugSources --stacktrace"
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
          gradle {
              useWrapper true
              // Build the app in release mode
              tasks 'clean assembleRelease'
          }
          echo "Signing APK"
          //withCredentials([certificate(aliasVariable: 'MulticertCertificate', credentialsId: 'certificateTest_P12_ID', keystoreVariable: 'certificate_content', passwordVariable: 'certificate_password')]) {
          signAndroidApks (
              keyStoreId: "certificateTest_P12_ID",
              keyAlias: "",
              apksToSign: "**/*-unsigned.apk"
              //uncomment the following line to output the signed APK to a separate directory as described above
              //signedApkMapping: [ $class: UnsignedApkBuilderDirMapping ]
              //uncomment the following line to output the signed APK as a sibling of the unsigned APK, as described above, or just omit signedApkMapping
              //you can override these within the script if necessary
              //androidHome: env.ANDROID_HOME
              //zipalignPath: env.ANDROID_ZIPALIGN
          )
      }
    }
  }
}

