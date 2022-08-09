pipeline {
  agent {
    docker {
      image 'maven:3.8.1-adoptopenjdk-11'
      args '-v /root/.m2:/root/.m2'
    }

  }
  stages {
    stage('Greeting') {
      steps {
        echo "Job [${JOB_NAME}]  - in the Workspace of [${WORKSPACE}]!"
        script {
          if(isUnix()) {
            sh "ls -lahR ${WORKSPACE} "
            sh "cd ${WORKSPACE} "
          } else {
            bat "cd ${WORKSPACE}"
          }
        }

      }
    }

    stage('Clone') {
      steps {
        dir(path: 'sub') {
          git(url: repoUrl, branch: branchName)
        }

      }
    }

    stage('SAST Scan') {
      steps {
        staticScan(branchName: branchName, credentialsId: 'ast-cx_sales_jurgen_elite_canary-cred', projectName: projectName)
      }
    }

  }
  environment {
    branchName = 'master'
    projectName = 'jenkins_linux/JavaVulnerableLab_for_AST_sca_resolver'
    projectTags = 'jvl_ast_sca_resolver'
    repoUrl = 'https://github.com/jvlstuff/JavaVulnerableLab_for_AST.git'
    projectGroup = 'myg'
    scanTimeout = 40
    scaName = 'ScaResolver'
    workspace = "${WORKSPACE}"
    scaResolverPath = ''
  }
}