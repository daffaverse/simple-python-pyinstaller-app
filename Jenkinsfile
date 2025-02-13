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
    
    stage('Manual Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    
    stage('Deploy') {
        sshagent(['gcp-ssh-key']) {
            sh '''
                ssh -o StrictHostKeyChecking=no c312b4ky1672@34.68.250.168 <<'EOF'
                source /home/c312b4ky1672/venv/bin/activate
                pyinstaller --onefile /home/c312b4ky1672/sources/add2vals.py
                chmod +x /home/c312b4ky1672/dist/add2vals
EOF
            '''
        }
        echo 'Aplikasi berhasil di Deploy'
        sleep time: 60, unit: 'SECONDS'
    }
}
