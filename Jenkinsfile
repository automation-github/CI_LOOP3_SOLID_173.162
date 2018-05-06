pipeline {
  agent any
  environment {
    target_cluster = '10.65.173.162'
  }
  stages {
    stage('installation') {
      steps {
            sh 'python2.7 /var/tmp/Nightswatch/deploy/upgrade_machines_sa.py -p $target_cluster --ininame \'/var/lib/jenkins/nightswatch/5.1/config.ini\''
          }
    }

    stage('Fragment_cache') {
      agent any
      steps {
        build (job: 'Fragment_cache/master', propagate: false)
      }
    }

    stage('Thumbnails') {
      agent any
      steps {
        build (job: 'Thumbnails/master', propagate: false)
      }
    }

    stage('AT_LRU_Cache') {
      agent any
      steps {
        build (job: 'AT_LRU_Cache/master', propagate: false)
      }
    }

    stage('copy xmls') {
      steps {
        sh '''scp -p root@$target_cluster:/tmp/Fragment_cache/*.xml .
scp -p root@$target_cluster:/tmp/Thumbnails/*.xml .
scp -p root@$target_cluster:/tmp/LRU_cache/*.xml .'''
      }
    }

    stage('check_cores') {
      steps {
        sh 'ssh root@$target_cluster "python2.7 /var/Nightswatch/deploy/find_cores.py"'
      }
    }

    stage('publish junit results') {
      steps {
        junit(testResults: '*.xml', healthScaleFactor: 1.0, allowEmptyResults: true)
      }
    }

  }
}
