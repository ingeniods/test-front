// CI test-front — npm build + imagen por docker-cli contra el socket docker
// montado en Jenkins (el front no usa Jib). Tag = version de package.json,
// que se mantiene alineada con los pom (1.0.0-SNAPSHOT).
pipeline {
  agent any
  stages {
    stage('build') {
      // el socket es del host: montar $PWD no funciona — se monta el VOLUMEN
      // jenkins-home y se entra al mismo path del workspace.
      steps { sh 'docker run --rm -v sandbox-jenkins-home:/var/jenkins_home -w "$PWD" node:22-alpine sh -c "npm install && npm run build"' }
    }
    stage('image') {
      when { anyOf { branch 'develop'; branch 'master' } }
      steps {
        script {
          def ver = sh(returnStdout: true, script:
            "grep -m1 '\"version\"' package.json | sed -E 's/.*\"version\": *\"([^\"]+)\".*/\\1/'").trim()
          currentBuild.displayName = ver
          withCredentials([string(credentialsId: 'ghcr-token', variable: 'GHCR_PAT')]) {
            withEnv(["IMG=ghcr.io/ingeniods/test-front:${ver}"]) {
              sh '''
                docker build -t "$IMG" .
                # carga al nodo kind (equivalente a `kind load`, sin el binario)
                docker save "$IMG" | docker exec -i agd-control-plane ctr -n k8s.io images import -
                # push a GHCR solo si hay PAT configurado
                if [ -n "$GHCR_PAT" ]; then
                  echo "$GHCR_PAT" | docker login ghcr.io -u ingeniods --password-stdin
                  docker push "$IMG"
                fi
              '''
            }
          }
        }
      }
    }
    stage('sonar') {
      steps {
        withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
          sh '''
            docker run --rm -v sandbox-jenkins-home:/var/jenkins_home -w "$PWD" \
              sonarsource/sonar-scanner-cli \
              -Dsonar.host.url=http://sonarqube:9000 \
              -Dsonar.token=$SONAR_TOKEN \
              -Dsonar.projectKey=test-front
          '''
        }
      }
    }
  }
}
