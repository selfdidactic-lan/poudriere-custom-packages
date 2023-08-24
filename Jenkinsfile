pipeline {
  agent {
    node {
      label 'FreeBSD'
    }
  }

  parameters {
    string(name: 'PORTS', defaultValue: '2023Q3', description: 'The ports branch to use.')
    boolean(name: 'UPDATE', defaultValue: true, description: 'Update ports tree before running?')
  }

  environment(
    JAIL = "13_2amd64"
    SET = "customset"
    PKG_LIST = "${WORKSPACE}/packages.list"
  )

  stages {
    stage('Cleanup') {
      sh "sudo poudriere pkgclean -j ${JAIL} -p ${PORTS} -f ${PKG_LIST}"
    }
    stage('Preparation') {
      sh """
        if [ -e /usr/local/poudriere/ports/${PORTS} ];then
            // Update ports tree
            sudo poudriere ports -p ${PORTS} -u
        else
            sudo poudriere ports -c -p ${PORTS} -B ${PORTS}
        fi
      """
    }
    stage('Build') {
      sh "sudo poudriere bulk -j ${JAIL} -p ${PORTS} -z ${SET} -f ${PKG_LIST}
    }
  }
}