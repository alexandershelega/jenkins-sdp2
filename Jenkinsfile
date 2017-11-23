properties([
        [$class: 'ParametersDefinitionProperty', parameterDefinitions: [
                [$class: 'hudson.model.BooleanParameterDefinition', name: 'create_deployment', defaultValue: true, description: 'Build docker images with :latest tag?'],
        ]
    ]
])

def update_image = params.create_deployment ? params.create_deployment : false

podTemplate(label: 'mypod' ,containers: [
        containerTemplate(image: 'docker', name: 'docker', command: 'cat', ttyEnabled: true),
        containerTemplate(name: 'kubectl', image: 'harbor.picsart.tools/analytics/kubectl:latest', ttyEnabled: true, command: 'cat')],
        imagePullSecrets: [ 'harbor' ],    
        volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]) {
    node('mypod') {
        stage('checkout') {
            git url: 'https://github.com/alexandershelega/jenkins-sdp2.git', credentialsId: 'a85736fd-a8d3-4686-9e6a-bc9fe9585767'
            }
            container('docker') {
                stage('Build Dockerfile') {
                    sh "echo test2"
                    sh "docker build --network=host --rm -t docker_test:$BRANCH_NAME-v$BUILD_NUMBER -f dockerfile.test ."
                }
            }
            container('kubectl') {
                stage('check kubectl') {
                    sh "echo $BRANCH_NAME ;  echo $BUILD_NUMBER"
                    sh "sed -i \"s/TTT/$BRANCH_NAME/g\" svc.yaml"
                    sh "cat svc.yaml"   
                }          
            stage("update image") {
                   if (params.create_deployment == true ) {
                       sh "kubectl get deployments"
                   } else {
                       sh "kubectl get deployments --namespace=monitoring"
                   }                     
                }
            }

        }
}
