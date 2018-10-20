node('haimaxy-jnlp'){
    stage('Clone'){
        echo "1.Prepare Stage"
	checkout scm
        
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            if (env.BRANCH_NAME != 'master') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
            }
        }
    }
        stage('Test'){
        echo "2.Test Stage"
    }
        stage('Build'){
        echo "3.Build Stage"
        sh "docker build -t reg.qiniu.com/petertest/jenkins-demo:${build_tag} ."
    }
            stage('Push'){
        echo "4.Push Stage"
        withCredentials([usernamePassword(credentialsId:'DockerQiNiu',passwordVariable:'dockerQiNiuPassword',usernameVariable:'dockerQiNiuUser')]){
            sh "docker login -u ${dockerQiNiuUser} -p ${dockerQiNiuPassword} reg.qiniu.com"
            sh "docker push reg.qiniu.com/petertest/jenkins-demo:${build_tag}"
        }
        
    }

        stage('Deploy'){
        echo "5. Deploy Stage"
        if (env.BRANCH_NAME == 'master') {
            input "确认要部署线上环境吗？a"
        }
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s.yaml"
        sh "kubectl apply -f k8s.yaml --record"
        
        
        
    }
}
