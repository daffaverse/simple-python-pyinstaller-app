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
    
    stage('Create Executable') {
        docker.image('cdrx/pyinstaller-linux:python2').inside {
            sh 'pyinstaller --onefile sources/add2vals.py'
            sh 'ls -la dist/'  // Verify artifact exists
        }
    }
    
    stage('Manual Approval') {
        input message: 'Proceed with deployment to cloud VM?'
    }
    
    stage('Deploy to Cloud') {
        sshagent(credentials: ['gcp-ssh-key']) {
            sh """
                ssh -o StrictHostKeyChecking=no c312b4ky1672@34.68.250.168 'mkdir -p /home/c312b4ky1672/app'
                scp -o StrictHostKeyChecking=no dist/add2vals c312b4ky1672@34.68.250.168:/home/c312b4ky1672/app/
                ssh -o StrictHostKeyChecking=no c312b4ky1672@34.68.250.168 'chmod +x /home/c312b4ky1672/app/add2vals'
            """
        }
        echo 'Artifact successfully deployed to cloud VM'
    }
}
