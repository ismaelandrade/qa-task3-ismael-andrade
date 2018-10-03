# qa-academy-performance-task3-ismael-andrade

## Run Jmeter non-gui mode
```
jmeter -n -t api_tests.jmx -l results-isma.jtl
```

## Generate dashboard
```
jmeter -g results-isma.jtl -o dashboard-isma
```



## Jenkins pipeline
```
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/wizelineacademy/qa-academy-performance.git']]])
            }
        }
        stage('Performance tests with Isma Todois token') {
            steps {
                withEnv(['JMETER_HOME=/Users/logics/proyectos/apache-jmeter-5.0/bin/jmeter']) {
                    sh '$JMETER_HOME/jmeter -n -t api_tests.jmx -l results-isma.jtl'
                }
            }
        }
        stage('Generate dashboard-isma') {
            steps {
                withEnv(['JMETER_HOME=/Users/logics/proyectos/apache-jmeter-5.0/bin']) {
                    sh '$JMETER_HOME/jmeter -g results-isma.jtl -o dashboard-isma'
                }
            }
        }
        stage('Publish') {
            steps {
                publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: 'dashboard-isma', reportFiles: 'index.html', reportName: 'Dashboard', reportTitles: ''])
                perfReport percentiles: '0,50,90,100', sourceDataFiles: 'results-isma.jtl'
            }
        }
    }
}
```
