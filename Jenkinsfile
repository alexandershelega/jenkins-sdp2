properties([
        [$class: 'ParametersDefinitionProperty', parameterDefinitions: [
                [$class: 'hudson.model.BooleanParameterDefinition', name: 'create_deployment', defaultValue: false, description: 'Create Deployment, if FALSE only image will updated'],
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
   
                }          
            stage("create deployment") {
                   if (params.create_deployment == true ) {
                       sh "sed -i \"s/TTT/nginx-$BRANCH_NAME/g\" svc.yaml"
                       sh "sed -i \"s/TTT/nginx-$BRANCH_NAME/g\" rc.yaml" 
                       sh "kubectl create -f svc.yaml"
                       sh "kubectl create -f rc.yaml"    
                       sh "kubectl get deployments"
                   } else {
                       sh "kubectl get deployments --namespace=monitoring"
                   }                     
                }
            }

        }
}
