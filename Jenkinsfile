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
        }
    }
    
    stage('Manual Approval') {
        input message: 'Proceed to Deploy?'
    }

    stage('Deploy') {
        docker.image('python:3.8').inside('-u root') {
            sh '''
                python -m pip install pyinstaller
                pyinstaller --onefile sources/add2vals.py
            '''
            
            archiveArtifacts 'dist/add2vals'
            
            sshagent(credentials: ['gcp-ssh-key']) {
                sh '''
                    scp -o StrictHostKeyChecking=no dist/add2vals c312b4ky1672@34.68.250.168:/home/c312b4ky1672/
                '''
            }
        }
        echo 'Apliaksi berhasil di Deploy'
        sleep time: 60, unit: 'SECONDS'
    }
}
