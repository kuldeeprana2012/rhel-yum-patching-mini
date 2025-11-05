pipeline {
  agent any
  environment {
    INVENTORY = "inventory/hosts.ini"
    PLAYBOOK  = "playbooks/patch.yml"
  }
  stages {
    stage('Checkout Code') {
      steps {
        checkout scm
      }
    }
    stage('Precheck') {
      steps {
        echo 'Checking connectivity to all RHEL nodes...'
        sh 'ansible all -i ${INVENTORY} -m ping'
      }
    }
    stage('Apply YUM Patches') {
      steps {
        echo 'Running YUM patch playbook...'
        sh 'ansible-playbook -i ${INVENTORY} ${PLAYBOOK}'
      }
    }
    stage('Verification') {
      steps {
        echo 'Verifying post-patch status...'
        sh 'ansible all -i ${INVENTORY} -a "uname -r"'
      }
    }
  }
  post {
    success {
      echo '✅ Patch applied successfully.'
    }
    failure {
      echo '❌ Patch failed. Triggering rollback...'
      sh 'ansible-playbook -i ${INVENTORY} playbooks/rollback.yml || true'
    }
  }
}
