node('haimaxy-jnlp') {
    stage('Prepare') {
        echo "1.Prepare Stage"
        checkout scm
        script {
        build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        if (env.BRANCH_NAME != 'master') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
        }
      }
    }
    stage('Test') {
        echo '2.Test stage'
    }
    stage('Bulid') {
        echo '3.Bulid Docker Image stage'
        sh "docker build -t zhaoziliang/jenkins-demo:${build_tag} ."
    } 
    stage('Push') {
        echo '4.Push Docker Image stage'
        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
        sh "docker login -u ${dockerHubUser} -p ${dockerHubPassword}"
        sh "docker push zhaoziliang/jenkins-demo:${build_tag}"
      }
    }
    stage('Deploy') {
        echo '5.Deploy stage'
        if (env.BRANCH_NAME == 'master') {
            input "确认要部署线上环境吗？"
        }
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s.yaml"
        sh "kubectl apply -f k8s.yaml"
    }
}
