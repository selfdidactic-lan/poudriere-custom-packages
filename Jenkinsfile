pipeline {
  agent {
    node {
      label 'FreeBSD'
    }
  }

  parameters {
    string(name: 'PORTS', defaultValue: '2023Q3', description: 'The ports branch to use.')
    booleanParam(name: 'UPDATE_BRANCH', defaultValue: true, description: 'Update ports tree before running?')
  }

  environment{
    JAIL = "13_2amd64"
    SET = "customset"
    PKG_LIST = "${WORKSPACE}/packages.list"
  }

  stages {
    stage('Cleanup') {
      steps {
        sh "sudo poudriere pkgclean -j ${JAIL} -p ${PORTS} -f ${PKG_LIST}"
      }
    }
    stage('Preparation') {
      steps {
        sh """
        if [ -e /usr/local/poudriere/ports/${PORTS} ] && [ -n ${UPDATE_BRANCH} ];then
            // Update ports tree
            echo "Updating branch ${PORTS}"
            sudo poudriere ports -p ${PORTS} -u
        else
            echo "Pulling new branch ${PORTS}"
            sudo poudriere ports -c -p ${PORTS} -B ${PORTS}
        fi
      """
      }
    }
    stage('Build') {
      steps {
        sh "sudo poudriere bulk -j ${JAIL} -p ${PORTS} -z ${SET} -f ${PKG_LIST}"
      }
    }
  }
}
