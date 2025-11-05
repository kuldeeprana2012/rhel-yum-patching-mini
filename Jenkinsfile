pipeline {
  agent any

  environment {
    INVENTORY = "inventory/hosts.ini"
    PLAYBOOK  = "playbooks/patch.yml"
    ANSIBLE_HOST_KEY_CHECKING = "False"
    ANSIBLE_SSH_ARGS = "-o StrictHostKeyChecking=no"
  }

  stages {

    stage('Checkout Code') {
      steps {
        echo '📦 Checking out repository...'
        checkout scm
      }
    }

    stage('Precheck') {
      steps {
        echo '🔍 Checking connectivity to all RHEL nodes...'
        sh 'ansible all -i ${INVENTORY} -m ping'
      }
    }

    stage('Apply YUM Patches') {
      steps {
        echo '🚀 Running YUM patch playbook...'
        sh 'ansible-playbook -i ${INVENTORY} ${PLAYBOOK}'
      }
    }

    stage('Verification') {
      steps {
        echo '🧾 Verifying post-patch kernel version...'
        sh 'ansible all -i ${INVENTORY} -a "uname -r"'
      }
    }
  }

  post {
    success {
      echo '✅ Patch applied successfully. All systems are up to date!'
    }
    failure {
      echo '❌ Patch failed. Triggering rollback...'
      sh 'ansible-playbook -i ${INVENTORY} playbooks/rollback.yml || true'
    }
  }
}
