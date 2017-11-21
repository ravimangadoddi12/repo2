stage 'build'
node ('LinuxNode'){
     git 'git@github.com:ravimangadoddi12/repo2.git'
     withEnv(["PATH+MAVEN=${tool 'apache-maven-3.5.0'}/bin"]) {
          sh "mvn -B clean package"
     }
     stash excludes: 'target/', includes: '**', name: 'source'
}
stage 'test'
parallel 'integration': {
     node ('LinuxNode') {
          unstash 'source'
          withEnv(["PATH+MAVEN=${tool 'apache-maven-3.5.0'}/bin"]) {
               sh "mvn clean verify"
          }
     }
}, 'quality': {
     node ('LinuxNode') {
          unstash 'source'
          withEnv(["PATH+MAVEN=${tool 'apache-maven-3.5.0'}/bin"]) {
               sh "mvn clean verify" //sonar:sonar
          }
     }
}
