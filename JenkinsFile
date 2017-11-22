podTemplate(label: 'mypod', containers: [containerTemplate(image: 'docker', name: 'docker', command: 'cat', ttyEnabled: true)],
        volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]) {
    node('mypod') {
        stage('checkout') {
            git url: 'https://github.com/alexandershelega/jenkins-sdp2.git', credentialsId: 'a85736fd-a8d3-4686-9e6a-bc9fe9585767'
            }
            container('docker') {
                stage('Build Dockerfile') {
                    sh "echo test"
                    sh "docker build --network=host --rm -t docker_test -f dockerfile.test ."
                }
            }
        }
}
