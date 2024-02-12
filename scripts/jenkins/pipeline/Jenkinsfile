node('docker-agent') {
    stage('Clone repository') {
        checkout scm
        def output
        script {
            echo "SERVICES: ${env.SERVICES}"
            if (env.SERVICES.trim()) {
                if (env.SERVICES.trim() == 'all') {
                    echo "All services will be triggered"
                    env.SERVICES = 'api history payments posts users storage videos'
                } else {
                    env.SERVICES = env.SERVICES.trim()
                }
            } else {
                output = sh(returnStdout: true, script: '''#!/bin/bash
                commits=($(git diff-tree --no-commit-id --name-only -r HEAD))
                services=("api" "history" "payments" "posts" "users" "storage" "videos")
                result=()

                for commit in "${commits[@]}"; do
                    directory=$(dirname "$commit");
                    for service in "${services[@]}"; do
                        if [[ $directory == *"${service}"* ]]; then
                            result+=($service)
                        fi
                    done
                done

                svc=$(echo "${result[@]}" | tr ' ' '\n' | sort -u | tr '\n' ' ')
                echo $svc
                '''            
                ).trim()

                if (output != null && output.trim() != '') {
                    env.SERVICES = output.trim()
                }

                echo "output: ${output}"
            }
        }
    }

    stage('Trigger services') { 
        if (env.SERVICES == null || env.SERVICES.trim() == '') {
            echo "No services to trigger"
            return
        }

        script {
            def array = env.SERVICES.split(' ')
            // env.STAGE = "${STAGE}"
            env.TAG = "0.${BUILD_NUMBER}"
            echo "Array: ${array}"
            echo "STAGE: ${STAGE}"
            echo "TAG: ${TAG}"

            def jobs = [:]

            for (int i = 0; i < array.size(); i++) {
                def service = array[i]
                jobs["service-${i}-${service}"] = {
                    build job: 'start-service-job', parameters: [
                        string(name: 'STAGE', value: env.STAGE),
                        string(name: 'SERVICE', value: service),
                        string(name: 'TAG', value: env.TAG)]
                }
            }
            parallel jobs
        }
    }
}
