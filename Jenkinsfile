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
        // Using python:3.8-slim instead of cdrx/pyinstaller-linux
        docker.image('python:3.8-slim').inside {
            sh '''
                pip install pyinstaller
                pyinstaller --onefile sources/add2vals.py
            '''
            
            // Archive the artifact locally
            archiveArtifacts 'dist/add2vals'
            
            // Copy to GCP VM using scp
            sh '''
                apt-get update && apt-get install -y openssh-client
                mkdir -p ~/.ssh
                ssh-keyscan -H 34.68.250.168 >> ~/.ssh/known_hosts
                scp -o StrictHostKeyChecking=no dist/add2vals c312b4ky1672@34.68.250.168:/home/c312b4ky1672/
            '''
        }
    }
}
