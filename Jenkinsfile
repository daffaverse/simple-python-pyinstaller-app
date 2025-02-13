pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/daffaverse/simple-python-pyinstaller-app.git'
            }
        }
        
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                    reuseNode true
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                    reuseNode true
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
                junit 'test-reports/results.xml'
            }
        }
        
        stage('Create Executable') {
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python2'
                    reuseNode true
                }
            }
            steps {
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
        }
        
        stage('Deploy to Cloud') {
            steps {
                sshagent(credentials: ['gcp-ssh-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no c312b4ky1672@34.68.250.168 'mkdir -p /home/c312b4ky1672/app'
                        scp -o StrictHostKeyChecking=no dist/add2vals c312b4ky1672@34.68.250.168:/home/c312b4ky1672/app/
                        ssh -o StrictHostKeyChecking=no c312b4ky1672@34.68.250.168 'chmod +x /home/c312b4ky1672/app/add2vals'
                    '''
                }
            }
        }
    }
}
