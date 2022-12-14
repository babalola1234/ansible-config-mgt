### Ansible dependencies to install
=================================
* For Mysql Database 
- ansible-galazy collection install community.mysql

* For Postgresql database 
- ansible-galazy collection install commuinity.mysql

### installing JAVA
=================
- sudo yum install java-11openjdk-devel -y

### Jenkins files for a quick start
===================================
---
   pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
     stage('Package') {
      steps {
        script {
          sh 'echo "Testing Package"'
        }
      }
    }
     stage('Deploy') {
      steps {
        script {
          sh 'echo "Testing Deploy"'
        }
      }
    }
     stage('Clean up') {
      steps {
        script {
          sh 'echo "Testing Clean Up"'
        }
      }
    }
    }
}
---

### Old Site.yml 
============================

---
- hosts: all
- import_playbook: ../static-assignments/common.yml

#- hosts: all 
#- import_playbook: ../static-assignments/common-del.yml

# - hosts: uat-webservers
# - import_playbook: ../static-assignments/uat-webservers.yml

  #- hosts: all
  #- name: Include dynamic variables 
  # import_playbook: ../static-assignments/common.yml 
  # include: ../dynamic-assignments/env-vars.yml
  # tags:
  # - always

# - hosts: webservers
# - name: Webserver assignment
#   import_playbook: ../static-assignments/webservers.yml

  #- hosts: lb
  #- name: "Loadbalancers assignment"
  #  import_playbook: ../static-assignments/loadbalancers.yml
  #when: load_balancer_is_required

 
 ###  Dependences to be installed
====================================

yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
yum install python3 python3-pip wget unzip git -y
python3 -m pip install --upgrade setuptools
python3 -m pip install --upgrade pip
python3 -m pip install PyMySQL
python3 -m pip install mysql-connector-python
python3 -m pip install psycopg2==2.7.5 --ignore-installed


Installing JAVA
====================================

sudo yum install java-11-openjdk-devel -y
open the bash profile
vi .bash_profile

paste the below in the bash profile
export JAVA_HOME=$(dirname (readlink (which javac))))) export PATH=$PATH:$JAVA_HOME/bin export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar

reload the bash profile
source ~/.bash_profile

Install php
=====================================

yum module reset php -y
yum module enable php:remi-7.4 -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm

Ansible dependencies to install
=====================================

*  For Mysql Database
- ansible-galaxy collection install community.mysql
*  For Postgresql Database
- ansible-galaxy collection install community.postgresql

Install composer for PHP
=====================================

` curl -sS https://getcomposer.org/installer | php `
` sudo mv composer.phar /usr/bin/composer ` 
- Verify Composer is installed or not
` composer --version `

### Install phpunit, phploc for PHP on Redhat
=====================================

sudo dnf --enablerepo=remi install php-phpunit-phploc
wget -O phpunit https://phar.phpunit.de/phpunit-7.phar
chmod +x phpunit
sudo yum install php-xdebug

### Install phpunit, phploc for PHP on ubuntu
============================================
- sudo apt update
- sudo apt install phploc
- sudo apt-get install -y phpunit-version

### Uninstall / Remove phploc package on ubuntu

- sudo apt remove phploc
- sudo apt autoclean && sudo apt autoremove

### for database connection for PHP
==================================== 

DB_CONNECTION=mysql 
DB_PORT=3306

- sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf 

- sudo yum install mysql -y

### instalatio of zip file

- sudo apt install zip
### Jenkinsfile for Quick Task
==================================

  pipeline {
    agent any

  stages {
    stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }

    stage('Package'){
      steps {
        script {
          sh 'echo "Packaging App" '
        }
      }
    }

    stage('Deploy'){
      steps {
        script {
          sh 'echo "Deploying to Dev"'
        }
      }

    }
    
    stage("clean Up"){
       steps {
        cleanWs()
     }
    }
     
    }
}

### sonar properties
=================================== 
sonar.host.url=http://3.125.17.131:9000/sonar/ sonar.projectKey=php-todo 
#----- Default source code encoding sonar.sourceEncoding=UTF-8 sonar.php.exclusions=/vendor/ sonar.php.coverage.reportPaths=build/logs/clover.xml sonar.php.tests.reportPath=build/logs/junit.xml

### Jenkinsfile for PHP Todo Job
===================================== 

pipeline 
     agent any {

stages {

 stage("Initial cleanup") {
      steps {
        dir("${WORKSPACE}") {
          deleteDir()
        }
      }
    }

stage('Checkout SCM') {
  steps {
        git branch: 'main', url: 'https://github.com/babalola1234/php-todo.git'
  }
}

stage('Prepare Dependencies') {
  steps {
         sh 'mv .env.sample .env'
         sh 'composer install'
         sh 'php artisan migrate'
         sh 'php artisan db:seed'
         sh 'php artisan key:generate'
  }
}

  stage('Execute Unit Tests') {
  steps {
         sh './vendor/bin/phpunit'
  } 
} stage('Code Analysis') { steps { sh 'phploc app/ --log-csv build/logs/phploc.csv'

} }

stage('Plot Code Coverage Report') { steps {

        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Lines of Code (LOC),Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Logical Lines of Code (LLOC)', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'A - Lines of code', yaxis: 'Lines of Code'
        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Directories,Files,Namespaces', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'B - Structures Containers', yaxis: 'Count'
        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Average Class Length (LLOC),Average Method Length (LLOC),Average Function Length (LLOC)', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'C - Average Length', yaxis: 'Average Lines of Code'
        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Cyclomatic Complexity / Lines of Code,Cyclomatic Complexity / Number of Methods ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'D - Relative Cyclomatic Complexity', yaxis: 'Cyclomatic Complexity by Structure'      
        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Classes,Abstract Classes,Concrete Classes', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'E - Types of Classes', yaxis: 'Count'
        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Methods,Non-Static Methods,Static Methods,Public Methods,Non-Public Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'F - Types of Methods', yaxis: 'Count'
        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Constants,Global Constants,Class Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'G - Types of Constants', yaxis: 'Count'
        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Test Classes,Test Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'I - Testing', yaxis: 'Count'
        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Logical Lines of Code (LLOC),Classes Length (LLOC),Functions Length (LLOC),LLOC outside functions or classes ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'AB - Code Structure by Logical Lines of Code', yaxis: 'Logical Lines of Code'
        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Functions,Named Functions,Anonymous Functions', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'H - Types of Functions', yaxis: 'Count'
        plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Interfaces,Traits,Classes,Methods,Functions,Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'BB - Structure Objects', yaxis: 'Count'

  }
}

stage('SonarQube Quality Gate') {
  when { branch pattern: "^develop*|^hotfix*|^release*|^main*", comparator: "REGEXP"}
    environment {
        scannerHome = tool 'SonarQubeScanner'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
        }
        timeout(time: 1, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
        }
    }
}

stage ('Package Artifact') {
steps {
        sh 'zip -qr php-todo.zip ${WORKSPACE}/*'
 }

}
stage ('Upload Artifact to Artifactory') {
      steps {
        script { 
             def server = Artifactory.server 'artifactory-server'                 
             def uploadSpec = """{
                "files": [
                  {
                   "pattern": "php-todo.zip",
                   "target": "PBL/php-todo",
                   "props": "type=zip;status=ready"

                   }
                ]
             }""" 

             server.upload spec: uploadSpec
           }
        }

    }
stage ('Deploy to Dev Environment') { steps { build job: 'ansible-config/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true } }

} 
}


#### jenkins from  scracth 
==================================

pipeline {
    agent any

    environment {
        ANSIBLE_CONFIG="${WORKSPACE}/deploy/ansible.cfg"
            }

    parameters {
        string (name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy configuration')
        }

    stages {
        stage("Initial cleanup") {
            steps {
                dir("${WORKSPACE}") {
                deleteDir()
                    }
                }
            }

        stage ('Checkout SCM') {
            steps{
                git branch: 'main', url: 'https://github.com/babalola1234/ansible-config.git'
            }
        }

        stage ('Prepare Ansible For Execution') {
            steps {
            sh 'echo ${WORKSPACE}' 
            sh 'sed -i "3 a roles_path=${WORKSPACE}/roles" ${WORKSPACE}/deploy/ansible.cfg'  
            }
        }

        stage ('Run Ansible playbook') {
            steps {
            ansiblePlaybook become: true, colorized: true, credentialsId: 'LAMP-SERVER', disableHostKeyChecking: true, installation: 'ansible', inventory: 'inventory/dev', playbook: 'playbooks/site.yml'
            }
        }

        stage ('Clean Workspace after build') {
            steps {
            cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
            }
        }
    }
}


### Dev inventory 
====================================
[nfs]
172.31.10.217 ansible_ssh_user='ec2-user'

[webservers]
172.31.0.203 ansible_ssh_user='ec2-user'
172.31.4.172 ansible_ssh_user='ec2-user'
172.31.2.22  ansible_ssh_user='ec2-user'

[db]
172.31.9.56  ansible_ssh_user='ubuntu'

[lb]
172.31.28.15 ansible_ssh_user='ubuntu'
172.31.24.120 ansible_ssh_user='ubuntu'

[uat-webservers]
172.31.46.201 ansible_ssh_user='ec2-user'
172.31.38.252 ansible_ssh_user='ec2-user'

[uat-lb]
172.31.46.201 ansible_ssh_user='ec2-user'
172.31.38.252 ansible_ssh_user='ec2-user'

[nginx]
172.31.28.15 ansible_ssh_user='ubuntu'

### ansible user add
# - name: "Create SonarQube system group"
#   group:
#     name: sonar
#     state: present

# - name: "Create SonarQube system user"
#   user:
#     name: sonar
#     comment: "user to run SonarQube"
#     state: present

# - name: Create Folder
#   file: 
#     path: /opt/sonarqube
#     owner: sonar
#     group: sonar
#     state: directory


### buntu@ip-172-31-22-8:~/ansible-config-mgt$ ansible-playbook -i inventory/ci.yml playbooks/site.yml 
--- 
<!-- ERROR! the role 'sonarqube' was not found in /home/ubuntu/ansible-config-mgt/playbooks/../static-assignments/roles:/home/ubuntu/.ansible/roles:/usr/share/ansible/roles:/etc/ansible/roles:/home/ubuntu/ansible-config-mgt/playbooks/../static-assignments

The error appears to be in '/home/ubuntu/ansible-config-mgt/static-assignments/sonar.yml': line 5, column 7, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:

  roles:
    - sonarqube
      ^ here
ubuntu@ip-172-31-22-8:~/ansible-config-mgt$ export ANSIBLE_CONFIG=/home/ubuntu/ansible-config-mgt/deploy/ansible.cfg --> ### the the export command if you encounter the above error.