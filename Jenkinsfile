pipeline {
    agent any
    tools {
        jdk 'jdk'
        maven 'maven'
    }

    stages {

        // Git Checkout
        stage('Git Checkout') {
            steps {
                git branch: 'master', changelog: false, poll: true, url: 'https://github.com/anantha3267/new.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }


        // Compile Stage
        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }

        // Run Test Cases
        stage('Test Cases') {
            steps {
                sh "mvn test"
            }
        }

        // // SonarQube Analysis
        // stage('Sonar analysis') {
        //     steps {
        //         script {
        //             // Use the SonarQube environment
        //             withSonarQubeEnv('SonarQube') {
        //                 sh "mvn clean package"
        //                 sh ''' mvn sonar:sonar \
        //                         -Dsonar.projectName=Scoreme \
        //                         -Dsonar.java.binaries=. \
        //                         -Dsonar.projectKey=Scoreme '''
        //             }
        //         }
        //     }
        // }

        // //Wait for SonarQube Quality Gate
        // stage('Quality Gate') {
        //     steps {
        //         timeout(time: 1, unit: 'HOURS') {
        //             script {
        //                 def qg = waitForQualityGate()
        //                 if (qg.status != 'OK') {
        //                     error "Pipeline aborted due to quality gate failure: ${qg.status}"
        //                 }
        //             }
        //         }
        //     }
        // }

//         stage('Check Directory Structure') {
//             steps {
//                 script {
//                     // Print the directory structure to verify correct path
//                     sh 'ls -R src'
//                 }
//             }
//         }

//         stage('Cyclomatic Complexity') {
//     steps {
//         // Run Lizard and generate the report
//         sh 'lizard src/**/*.java  --xml > complexity-report.xml'

//         // Publish the report in Jenkins
//         // publishHTML(target: [
//         //     reportName: 'Cyclomatic Complexity',
//         //     reportDir: '.',
//         //     reportFiles: 'complexity-report.xml'
//         // ])
//     }
// }

        stage('OWASP') {
            steps {
                dependencyCheck additionalArguments: '--format HTML', odcInstallation: 'Dependency check',
                 stopBuild: true // Stop on any critical vulnerabilities
                 
            }     
        }
        // JaCoCo Code Coverage Report
        stage('JaCoCo Report') {
            steps {
                // Run JaCoCo and generate the report
                sh "mvn clean test jacoco:report"
            }
        }

    }
    post {
    always {
        // Publish the Dependency-Check HTML report
        publishHTML(
            target: [
                reportName: 'Dependency-Check Report',
                reportDir: '', // No need to specify a directory if the report is in the workspace root
                reportFiles: 'dependency-check-report.html', // The HTML report generated
                keepAll: true
            ]
        )

        // Optionally, you can publish the JaCoCo HTML report in Jenkins
        publishHTML(
            target: [
                reportName: 'JaCoCo Report',
                reportDir: 'target/site/jacoco',
                reportFiles: 'index.html'
            ]
        )
    }
}

}
