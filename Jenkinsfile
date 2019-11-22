pipeline {
    agent {
        label 'docker'
    } 
    environment {
        ANDROID_HOME = tool name: 'androidSdk'
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

        stage("Clean")
        {
            steps{
                sh '''gradle --version'''
                sh './gradlew clean' //run a gradle task
                echo "The build stage passed..."
            }
        }
        stage("Check Gradle Tasks") {
            steps{
                echo "Gradle Tasks..."
                sh './gradlew tasks'
                echo "----------------------------------------------"
            }
        }
        // stage('Compile') {
        //     steps {
        //       // Compile the app and its dependencies
        //       sh './gradlew compileDebugSources --stacktrace'
        //     }
        // }
        // stage('Static analysis') {
        //     steps {
        // // Run Lint and analyse the results
        //         sh './gradlew lintDebug'
        //         androidLint pattern: '**/lint-results-*.xml'
        //     }
        // }
        stage("BUILD") {
            steps{
                echo "Assemble"
                sh './gradlew build assembleRelease' //run a gradle task

            }
        }
// -------------------Correct Pipeline workflow -----------------------
//   options {
//     // Stop the build early in case of compile or test failures
//     skipStagesAfterUnstable()
//   }
//   stages {
    stage('Compile') {
      steps {
        // Compile the app and its dependencies
        sh './gradlew compileDebugSources'
      }
    }
    stage('Unit test') {
      steps {
        // Compile and run the unit tests for the app and its dependencies
        sh './gradlew testDebugUnitTest testDebugUnitTest'

        // Analyse the test results and update the build result as appropriate
        junit '**/TEST-*.xml'
      }
    }
    stage('Build APK') {
      steps {
        // Finish building and packaging the APK
        sh './gradlew assembleDebug'

        // Archive the APKs so that they can be downloaded from Jenkins
        archiveArtifacts '**/*.apk'
      }
    }
    stage('Static analysis') {
      steps {
        // Run Lint and analyse the results
        sh './gradlew lintDebug'
        androidLint pattern: '**/lint-results-*.xml'
      }
    }
    stage('Deploy') {
      when {
        // Only execute this stage when building from the `beta` branch
        branch 'beta'
      }
      environment {
        // Assuming a file credential has been added to Jenkins, with the ID 'my-app-signing-keystore',
        // this will export an environment variable during the build, pointing to the absolute path of
        // the stored Android keystore file.  When the build ends, the temporarily file will be removed.
        SIGNING_KEYSTORE = credentials('my-app-signing-keystore')

        // Similarly, the value of this variable will be a password stored by the Credentials Plugin
        SIGNING_KEY_PASSWORD = credentials('my-app-signing-password')
      }
      steps {
        // Build the app in release mode, and sign the APK using the environment variables
        sh './gradlew assembleRelease'

        // Archive the APKs so that they can be downloaded from Jenkins
        archiveArtifacts '**/*.apk'

        // Upload the APK to Google Play
        androidApkUpload googleCredentialsId: 'Google Play', apkFilesPattern: '**/*-release.apk', trackName: 'beta'
      }
//       post {
//         success {
//           // Notify if the upload succeeded
//           mail to: 'beta-testers@example.com', subject: 'New build available!', body: 'Check it out!'
//         }
//       }
//     }
//   }
//   post {
//     failure {
//       // Notify developer team of the failure
//       mail to: 'android-devs@example.com', subject: 'Oops!', body: "Build ${env.BUILD_NUMBER} failed; ${env.BUILD_URL}"
//     }
//   }

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
}
