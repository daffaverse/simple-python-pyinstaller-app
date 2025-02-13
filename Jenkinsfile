node {
    stage('Checkout') {
        git branch: 'master', url: 'https://github.com/daffaverse/simple-python-pyinstaller-app.git'
    }
    
    docker.image('python:2-alpine').inside {
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    
    docker.image('qnib/pytest').inside {
        stage('Test') {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }
    
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    
    docker.image('cdrx/pyinstaller-linux:python2').inside {
        stage('Deploy') {
            sh 'pyinstaller --onefile sources/add2vals.py'
            archiveArtifacts 'dist/add2vals'
            
            sshagent(['gcp-ssh-key']) {
                sh """
                    scp -o StrictHostKeyChecking=no dist/add2vals c312b4ky1672@34.59.141.57:/home/c312b4ky1672/python-app/
                    ssh -o StrictHostKeyChecking=no c312b4ky1672@34.59.141.57 'chmod +x ~/python-app/add2vals'
                """
                sleep 60
            }
        }
    }
}
