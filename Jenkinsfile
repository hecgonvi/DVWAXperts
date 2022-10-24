environment {
    region = "us-east-1"
    docker_repo_uri = "547850580937.dkr.ecr.us-east-1.amazonaws.com/test-app"
    task_def_arn = "arn:aws:ecs:us-east-1:xxxxxxxxxxxx:task-definition/first-run-task-definition"
    cluster = "cluster-uno"
    exec_role_arn = "arn:aws:iam::547850580937:instance-profile/jenkins"
}
stage('Deploy') {
    steps {
        // Override image field in taskdef file
        sh "sed -i 's|{{image}}|${docker_repo_uri}:${commit_id}|' taskdef.json"
        // Create a new task definition revision
        sh "aws ecs register-task-definition --execution-role-arn ${exec_role_arn} --cli-input-json file://taskdef.json --region ${region}"
        // Update service on Fargate
        sh "aws ecs update-service --cluster ${cluster} --service sample-app-service --task-definition ${task_def_arn} --region ${region}"
    }
}
