pipeline {
    agent any
    tools {
        jdk 'java_home'
        maven 'maven_home' 
    }
    options { 
        buildDiscarder(logRotator(numToKeepStr: '5')) 
        timeout(time: 120, unit: 'SECONDS')
        
    }
    triggers { 
        cron('H/60 * * * *') 
    }
    parameters{
        booleanParam(name: 'TEST', defaultValue: true, description: 'Mark as true if deployment needed')
        gitParameter(branch: '',branchFilter: '.*',defaultValue: 'master',description: '',name: 'branch',quickFilterEnabled: false,selectedValue: 'NONE',sortMode: 'NONE',tagFilter: '*',type: 'PT_BRANCH')
    }
stages{
    stage('checkout'){
        steps{
            echo "I'm checking out"
            checkout([$class: 
                'GitSCM', 
                branches: [[name: "${params.branch}"]], 
                extensions: [], 
                userRemoteConfigs: [[url: 'https://github.com/rohithhm/cobertura-example.git']]
            ])
        }
    } 
    stage('testing'){
        when{
		expression{params.TEST}
	    }
        steps{
            echo "I'm testing"
            sh '''
                mvn clean cobertura:cobertura -Dcobertura.report.formats=xml
            '''
        }
    }
    stage('cobertura publish'){ 
        steps{
            echo "cobertura publish"
            cobertura autoUpdateHealth: false, 
            autoUpdateStability: false, 
            coberturaReportFile: '**/target/site/cobertura/coverage.xml', 
            conditionalCoverageTargets: '70, 60, 50', 
            fileCoverageTargets: '70, 60, 50', 
            lineCoverageTargets: '80, 70, 60', 
            maxNumberOfBuilds: 0, 
            methodCoverageTargets: '80, 60, 40', 
            onlyStable: false, 
            packageCoverageTargets: '80, 60, 40', 
            sourceEncoding: 'ASCII', 
            zoomCoverageChart: false
        }
    }
    stage('build'){
            steps{
            echo "Building"
            // sh """
            // mvn clean install
            // """
        }
    }
    stage('deploy'){
        steps{
            echo "Deploying"
            // sh """
            // sudo cp /var/lib/jenkins/workspace/LOGIN/Login_Pipeline/target/java-tomcat-maven-example.war /var/lib/tomcat/webapps/
            // """
        }
    }
}
post{
    always{
        echo "Build is completed"
        cleanWs()
    }
}
}
