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
        input massage: 'Lanjutkan ke tahap Deploy?'
    }

    docker.image('cdrx/pyinstalller-linux:python2').inside {
        stage('Deploy') {
            sh 'pyinstaller --onefile sources/add2vals.py'
            archiveArtifacts 'dist/add2vals'
            sleep 60
        }
    }
}