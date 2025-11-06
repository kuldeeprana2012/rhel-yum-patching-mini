pipeline {
  agent any
  environment {
    PATH = "/usr/local/bin:/usr/bin:/bin"
    INVENTORY = "${WORKSPACE}/inventory/hosts.ini"
    PLAYBOOK  = "${WORKSPACE}/playbooks/patch.yml"
    ANSIBLE_HOST_KEY_CHECKING = "False"
    ANSIBLE_SSH_ARGS = "-o StrictHostKeyChecking=no"
  }

  stages {
 stage('Checkout') {
  steps {
    checkout([$class: 'GitSCM',
              branches: [[name: '*/main']],
              userRemoteConfigs: [[url: 'https://github.com/kuldeeprana2012/rhel-yum-patching-mini.git']],
              extensions: [
                [$class: 'WipeWorkspace'],
                [$class: 'CleanBeforeCheckout']
              ]])
    sh 'git log -1 --oneline'
  }
}



    stage('Precheck') {
      steps {
        echo "🔍 Checking connectivity to all RHEL nodes..."
        sh 'ansible all -i ${INVENTORY} -m ping'
      }
    }

    stage('Apply YUM Patches') {
      steps {
        echo "🚀 Running YUM patch playbook..."
        sh 'ansible-playbook -i ${INVENTORY} ${PLAYBOOK}'
      }
    }
  }

  post {
    success {
      echo "✅ Patching completed successfully."
    }
    failure {
      echo "❌ Patching failed. Rolling back..."
      sh 'ansible-playbook -i ${INVENTORY} playbooks/rollback.yml || true'
    }
  }
}
