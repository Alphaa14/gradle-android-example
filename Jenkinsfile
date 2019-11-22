pipeline {
    agent {
        label 'docker'
    } 
    environment {
        ANDROID_HOME = tool name: 'androidSdk'
        // GRADLE_OPTS = tool name: "alpine-pkg-glibc"
        
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

        // stage("Clean")
        // {
        //     steps{
        //         //sh "gradle --version"
        //         // sh "./gradlew clean" //run a gradle task
        //         echo "The build stage passed..."
        //     }
        // }
        // stage("Check Gradle Tasks") {
        //     steps{
        //         echo "Gradle Tasks..."
        //         // sh "./gradlew tasks"
        //         echo "----------------------------------------------"
        //     }
        // }
// -------------------Correct Pipeline workflow -----------------------

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
        junit "**/TEST-*.xml"
      }
    }
    stage("Build APK") {
      steps {
        // Finish building and packaging the APK
        sh "./gradlew assembleDebug"

        // Archive the APKs so that they can be downloaded from Jenkins
        archiveArtifacts "**/*.apk"
      }
    }
    stage("Static analysis") {
      steps {
        // Run Lint and analyse the results
        sh "./gradlew lintDebug"
        //androidLint pattern: '**/lint-results-*.xml'
      }
    }
    stage("build unsigned apk"){
      steps {
        sh './gradlew clean assembleRelease'
      }
    }
    stage("sign apk"){
      //withCredentials([certificate(aliasVariable: '', credentialsId: 'certificate_P12_ID', keystoreVariable: 'certificate_content', passwordVariable: 'certificate_password')]) {
        steps {
        signAndroidApks (
        keyStoreId: "81c76f5a-8868-4c14-b067-ed36bf497a8e",
        keyAlias: "",
        apksToSign: "**/*-unsigned.apk"
        )}
      //}
    }
    }
    // stage('Deploy') {
    //   environment {
    //     // Assuming a file credential has been added to Jenkins, with the ID 'my-app-signing-keystore',
    //     // this will export an environment variable during the build, pointing to the absolute path of
    //     // the stored Android keystore file.  When the build ends, the temporarily file will be removed.
    //     SIGNING_KEYSTORE = credentials('certificate_P12_ID')

    //     // Similarly, the value of this variable will be a password stored by the Credentials Plugin
    //     SIGNING_KEY_PASSWORD = credentials('certificate_content')
    //   }
    //   steps {
    //     // Build the app in release mode, and sign the APK using the environment variables
    //     sh './gradlew clean assembleRelease'

    //     // Archive the APKs so that they can be downloaded from Jenkins
    //     archiveArtifacts '**/*.apk'

    //     // Upload the APK to Google Play
    //     // androidApkUpload googleCredentialsId: 'Google Play', apkFilesPattern: '**/*-release.apk', trackName: 'beta'
    //   }
    // }
  //  }
    post {
      success {
          // Notify if the upload succeeded
          mail to: 'jorgeribeiro14@gmail.com', subject: 'New build available!', body: 'Check it out!'
        }
      }
    post {
      failure {
       // Notify developer team of the failure
       mail to: 'jorgeribeiro14@gmail.com', subject: 'Oops!', body: "Build failed;"  /* ${env.BUILD_NUMBER}  ${env.BUILD_URL}*/
     }
   }

// -----------------------------------/Correct Pipeline Workflow----------------------------------------------------------




        // stage("ASSEMBLE") {
        //     steps{
        //         echo "Assemble"
        //         sh './gradlew assemble' //run a gradle task

        //     }
        // }
        // stage('Deploy') {
            // steps {
            //   script {                                                        
                // if (currentBuild.result == null         
                    // || currentBuild.result == 'SUCCESS') {  
                //    if(env.BRANCH_NAME ==~ /master/) {
                    //  Deploy when the committed branch is master (we use fastlane to complete this)     
                        //  sh 'fastlane app_deploy' 
                    // }
                //   }
                // }
            // }
        // }
        // post {
            // always {
            //   archiveArtifacts(allowEmptyArchive: true, artifacts: 'app/build/outputs/apk/production/release/*.apk')      // And kill the emulator?
            //   sh 'adb emu kill'
            // }
        // }
        // stage("Signing APK"){
            // steps{
                // gradle {
                    // rootBuildScriptDir 'myApp'
                    // useWrapper true
                    // tasks 'clean assembleRelease'
                // }
                // echo "Signing APK"
                // withCredentials([certificate(aliasVariable: 'MulticertCertificate', credentialsId: 'certificate_P12_ID', keystoreVariable: 'certificate_content', passwordVariable: 'certificate_password')]) {
                    // signAndroidApks (
                        // keyStoreId: "myApp.signerKeyStore",
                        // keyAlias: "myTeam",
                        // apksToSign: "**/*-unsigned.apk"
                        // uncomment the following line to output the signed APK to a separate directory as described above
                        // signedApkMapping: [ $class: UnsignedApkBuilderDirMapping ]
                        // uncomment the following line to output the signed APK as a sibling of the unsigned APK, as described above, or just omit signedApkMapping
                        // you can override these within the script if necessary
                        // androidHome: env.ANDROID_HOME
                        // zipalignPath: env.ANDROID_ZIPALIGN
                    // )
                // }
            // }
        // }
  }
