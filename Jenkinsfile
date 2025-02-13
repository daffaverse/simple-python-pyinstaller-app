pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/daffaverse/simple-python-pyinstaller-app.git'
            }
        }
        
        stage('Build and Test') {
            steps {
                script {
                    // Build
                    docker.image('python:2-alpine').inside {
                        sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                    }
                    
                    // Test
                    docker.image('qnib/pytest').inside {
                        sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
                        junit 'test-reports/results.xml'
                    }
                    
                    // Create executable
                    docker.image('cdrx/pyinstaller-linux:python2').inside {
                        sh '''
                            pyinstaller --onefile sources/add2vals.py
                            ls -la dist/
                        '''
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sshagent(['gcp-ssh-key']) {
                    sh '''
                        scp -o StrictHostKeyChecking=no dist/add2vals c312b4ky1672@34.68.250.168:/home/c312b4ky1672/app/
                        ssh -o StrictHostKeyChecking=no c312b4ky1672@34.68.250.168 'chmod +x /home/c312b4ky1672/app/add2vals'
                    '''
                }
            }
        }
    }
}
