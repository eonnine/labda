def label = 'watcha-webapp'

podTemplate(label: label, containers: [
    containerTemplate(name: 'git', image: 'alpine/git', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl', command: 'cat', ttyEnabled: true)
],
volumes: [
  hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
]) {
    echo "first@@@@@@@@@@@@@@"
    node(label) {
        echo "first@@@@@@@@@@@@@@end"
        def dockerhubUrl = 'jeg910716/watcha-webapp-test'
        def credentialDockerId = 'docker'
        def credentialGithubId = 'github'
        echo "second@@@@@@@@@@@@@@"

        stage('Clone repository') {
            container('git') {
                // https://gitlab.com/gitlab-org/gitlab-foss/issues/38910
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/master']],
                    userRemoteConfigs: [
                        [url: 'http://192.168.194.132/root/sampleapp.git', credentialsId: "$credentialGithubId"]
                    ],
                ])
            }
        }

        stage('Clone repository') {
            echo "third@@@@@@@@@@@@@@"
            checkout scm
            echo "third@@@@@@@@@@@@@@end"
        }
        stage('Create Docker images') {
            container('docker') {
                echo "fourth@@@@@@@@@@@@@@"
                withCredentials('https://registry.hub.docker.com', "$credentialDockerId") {
                sh """
                    docker build -t $dockerhubUrl:dev ./
                    docker push -t $dockerhubUrl:dev
                    """
                }
                echo "fourth@@@@@@@@@@@@@@end"
            }
        }
        stage('Run kubectl') {
            container('kubectl') {
                sh "kubectl get pods"
            }
        }
    }
}