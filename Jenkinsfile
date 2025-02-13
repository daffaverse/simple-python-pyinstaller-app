node {
    // Stage Checkout: Mengambil source code dari Git
    stage('Checkout') {
        git branch: 'master', url: 'https://github.com/daffaverse/simple-python-pyinstaller-app.git'
    }
    
    // Stage Build: Melakukan compile sederhana menggunakan Docker image python:2-alpine
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    
    // Stage Test: Menjalankan unit test menggunakan Docker image qnib/pytest
    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        // Mengumpulkan hasil test ke Jenkins
        junit 'test-reports/results.xml'
    }
    
    // Stage Deliver: Build binary menggunakan pyinstaller, stash artifact, dan archive hasil build
    stage('Deliver') {
        docker.image('cdrx/pyinstaller-linux:python2').inside {
            // Pastikan pyinstaller menghasilkan file di folder "dist" dengan nama "add2vals"
            sh 'pyinstaller --onefile sources/add2vals.py'
            // Opsi: cek apakah file sudah dibuat
            sh 'ls -la dist/'
            // Stash artifact sehingga dapat digunakan di stage Deploy
            stash name: 'artifact', includes: 'dist/add2vals'
        }
        // Archive artifact agar tersimpan di Jenkins
        archiveArtifacts artifacts: 'dist/add2vals'
    }
    
    // Stage Deploy: Unstash artifact dan deploy ke remote host
    stage('Deploy') {
        // Unstash file artifact ke workspace
        unstash 'artifact'
        // Opsional: cek kembali file artifact
        sh 'ls -la dist/'
        // Deploy artifact ke remote host menggunakan SCP dan SSH
        sshagent(['gcp-ssh-key']) {
            sh '''
                scp -o StrictHostKeyChecking=no dist/add2vals c312b4ky1672@34.68.250.168:/home/c312b4ky1672/app/
                ssh -o StrictHostKeyChecking=no c312b4ky1672@34.68.250.168 'chmod +x /home/c312b4ky1672/app/add2vals'
            '''
        }
        echo 'Aplikasi berhasil di Deploy'
        sleep time: 60, unit: 'SECONDS'
    }
}
