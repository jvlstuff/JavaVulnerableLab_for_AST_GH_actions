pipeline {
  agent {
    docker {
      image 'maven:3.8.1-adoptopenjdk-11'
      args '-v /root/.m2:/root/.m2'
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
    stage('SAST Scan') {
      steps {
        staticScan(branchName: branchName, credentialsId: 'ast-cx_sales_jurgen_elite_canary-cred', projectName: projectName)
      }
    }
  }
}

void scaResolverInstallation(String scaDir) {
    fileName = scaName + '.tar.gz'
    filePath = "${scaDir}/${fileName}"
    scaResolverDir = "${scaDir}"
    scaResolverPath = "${scaResolverDir}/${scaName}"
    
    fileOperations([
        steps.fileDeleteOperation(
            includes: fileName,
            excludes: ''
        ),
        fileDownloadOperation(
            url: 'https://sca-downloads.s3.amazonaws.com/cli/latest/ScaResolver-linux64.tar.gz',
            userName: '',
            password: '',
            targetLocation: scaResolverDir,
            targetFileName: fileName
        ),
        fileUnTarOperation(
            filePath : filePath,
            targetLocation : scaResolverDir,
            isGZIP : true
        )
    ])
    
}
void staticScan(Map args) {
    scaResolverInstallation(workspace)
     

    dir('sub') {
        checkmarxASTScanner additionalOptions: "--debug --async --project-tags ${projectTags} --project-groups ${projectGroup} --scan-timeout ${scanTimeout} --sca-resolver ${scaResolverPath} --sca-resolver-params \" --log-level Verbose --scan-containers true \" ",
        checkmarxInstallation: 'ast-cli-automatical',
        baseAuthUrl: 'https://deu.iam.checkmarx.net',
        serverUrl: 'https://deu.ast.checkmarx.net',
        branchName: args.branchName,
        credentialsId: args.credentialsId,
        projectName: args.projectName,
        tenantName: 'cx_sales_jurgen_elite_canary',
        useAuthenticationUrl: true,
        useOwnAdditionalOptions: true,
        useOwnServerCredentials: true
    }
}
