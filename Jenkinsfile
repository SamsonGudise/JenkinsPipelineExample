node {

    stage('Checkout') {
        checkout scm
    }
    stage ('Build Image') {
        if ('develop' == scm.branches[0].name || 'master' == scm.branches[0].name) {
            docker.withRegistry('','dockerHub') {
                def latestImage = docker.build("samsonbabu/nginx-jenkins-demo:${env.BUILD_ID}")
                latestImage.push()
                latestImage.push('latest')
            }
        }
    }

    stage ('Deploy to kube cluster') {
        if ('develop' == scm.branches[0].name || 'master' == scm.branches[0].name) {
            echo "aws eks update-kubeconfig --kubeconfig /tmp/kubeconfig --profile=dev"
            echo "kubectl --kubeconfig=/tmp/kubeconfig apply -f service.yaml"
            echo "export IMAGE_TAG=${env.BUILD_ID}; envsubst < deployment.yaml | kubectl --kubeconfig=/tmp/kubeconfig apply -f  -"
        }
    }

    stage ('Running test') {
        echo "${BRANCH_NAME}"
        if ("${BRANCH_NAME}" ==~ /PR-[0-9]+/) {
            sh "test.sh"
        }
    }

    stage('promote') {
        if ('master' == scm.branches[0].name) {
            echo "aws eks update-kubeconfig --kubeconfig /tmp/kubeconfig --profile=qa"
            echo "kubectl --kubeconfig=/tmp/kubeconfig apply -f service.yaml"
            echo "export IMAGE_TAG=${env.BUILD_ID}; envsubst < deployment.yaml | kubectl --kubeconfig=/tmp/kubeconfig apply -f  -"
        }
    }
}