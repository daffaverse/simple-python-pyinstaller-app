node {
    stage('Checkout') {
        git branch: 'master', url: 'https://github.com/daffaverse/simple-python-pyinstaller-app.git'
    }
    
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    
    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            junit 'test-reports/results.xml'
        }
    }
    
    stage('Deliver') {
        docker.image('cdrx/pyinstaller-linux:python2').inside {
            sh 'pyinstaller --onefile sources/add2vals.py'
        }
    }
    
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    
    stage('Deploy') {
        sshagent(credentials: ['gcp-ssh-key']) {
            sh """
                scp -o StrictHostKeyChecking=no dist/add2vals c312b4ky1672@34.68.250.168:/home/c312b4ky1672/app/
                ssh -o StrictHostKeyChecking=no c312b4ky1672@34.68.250.168 'chmod +x /home/c312b4ky1672/app/add2vals'
                ssh -o StrictHostKeyChecking=no c312b4ky1672@34.68.250.168 'cd /home/c312b4ky1672/app && python3 -m http.server 8000 &'
            """
        }
        echo 'Aplikasi berhasil di Deploy'
    }
}
