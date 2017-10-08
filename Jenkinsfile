node {
    
    checkout scm

    stage ('Build Mule artifact'){
        sh 'cd app;mvn clean package -DskipTests=true'
    }
 
    docker.withRegistry('', 'docker-hub-login') {
        stage 'Backend Build'
        def backendImg = docker.build("vishalbiyani/mule-app:${env.BUILD_TAG}", '.')
        backendImg.push();
        backendImg.push('latest');

        stage 'FrontEndBuild'
        def frontendImage = docker.build("vishalbiyani/mule-ui:${env.BUILD_TAG}",'front_end')
        frontendImage.push();
        frontendImage.push('latest');
    }

    stage('Deploy Application') {

        switch(env.BRANCH_NAME) {

        case "blue-green" :


            // Deploy Back-end

            sh 'kubectl apply -f app-deployment-test.yaml'

            sh 'sed 's/blue/green/' app-service-test.yaml'

            sh 'kubectl apply -f app-service-test.yaml'

            // Deploy Front-end

            sh 'cd front_end;kubectl apply -f app-deployment-test.yaml'

            sh 'cd front_end; sed 's/blue/green/' app-service-test.yaml'

            sh 'cd front_end;kubectl apply -f app-service-test.yaml'

            break;

        case "master" :

            // Deploy Back-end

            sh 'kubectl apply -f app-deployment.yaml'
            sh 'kubectl apply -f app-service.yaml'

            // Deploy Front-end
            sh 'cd front_end;kubectl apply -f app-deployment.yaml'
            sh 'cd front_end;kubectl apply -f app-service.yaml'

            break;

        }
    }
}