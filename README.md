# jenkinsfile-cheatsheet
Just another cheat sheet of Jenkins pipeline script snippets

## Environment Variables

Environment variables are exposed via the global variable `env`

```Groovy
    println env.WORKSPACE
```    

see also [Jenkins Handbook: Working with the Environment](https://jenkins.io/doc/book/pipeline/jenkinsfile/#working-with-the-environment)

A list of environment variables automatically set by Jenkins on each build can be found at
[Jenkins Set Environment Variables](https://wiki.jenkins-ci.org/display/JENKINS/Building+a+software+project#Buildingasoftwareproject-JenkinsSetEnvironmentVariables) 
        
### Setting environment variables

Setting an environment variable within a Jenkins pipeline can be done with the 
[withEnv step](https://jenkins.io/doc/pipeline/steps/workflow-basic-steps/#code-withenv-code-set-environment-variables)
from the [Basic Steps Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Pipeline+Basic+Steps+Plugin)
            
```Groovy
    withEnv(["RANCHER_URL=${conf.rancherURL}"]) {
        println(env.RANCHER_URL)
    }
```


## Reading a property file

A property file can be read using the [readProperties step](https://jenkins.io/doc/pipeline/steps/pipeline-utility-steps/#code-readproperties-code-read-properties-from-files-in-the-workspace-or-text) 
from the [Pipeline Utility Steps Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Pipeline+Utility+Steps+Plugin)
 
```Groovy
    def conf = readProperties file: "${env.WORKSPACE}@script/build.properties"
```

However if you need to access a `properties` file from within your `scripts` directory, where your `Jenkinsfile` is located,
you need to use the absolute path to load it. But be aware, that it only works as in the example as long as you don't run
build job in multiple times in parallel.
See also [Stackoverflow: How can I reference the Jenkinsfile directory, with Pipeline?](http://stackoverflow.com/questions/37353963/how-can-i-reference-the-jenkinsfile-directory-with-pipeline) 


## Checking out from a SCM system

Their are various ways to checkout source code from a SCM system. You can 
use the generic `checkout` step from the [Pipeline SCM Step Plugin](https://github.com/jenkinsci/workflow-scm-step-plugin), 
specifying the specific SCM driver class, e.g.

```Groovy
    checkout scm: [$class: 'SubversionSCM',
                   locations: [[credentialsId: '6a4ef5df-461b-4a10-8bc3-f1e68b06bb5c',
                                depthOption: 'infinity',
                                ignoreExternalsOption: true,
                                clean: true,
                                remote: 'https://subversion.whatever.com/my-repo/']],
                   poll: true]
```                       

The [Git plugin](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin) and 
the [Subversion Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Subversion+Plugin) are providing specific steps as shorthand for the genereic `checkout` step, e.g.
    
```Groovy
    if (!fileExists('jenkinsfile-cheatsheet')) {
        sh 'mkdir jenkinsfile-cheatsheet'
    }
    dir('jenkinsfile-cheatsheet') {
        git url: 'git@github.com:cwkrebs/jenkinsfile-cheatsheet.git', credentialsId: conf.gitCredentialsId
        ... 
    }
```
see also [git steo reference](https://jenkins.io/doc/pipeline/steps/git/)

```Groovy
    svn url: 'https://subversion.whatever.com/my-repo/'
```
see also [svn step reference](https://jenkins.io/doc/pipeline/steps/subversion/)
            
## Using external Groovy class 

* https://github.com/jenkinsci/workflow-cps-global-lib-plugin/blob/master/README.md
* http://stackoverflow.com/questions/40685744/jenkins-pipeline-jenkinsfile-load-external-groovy-class
* http://docs.groovy-lang.org/latest/html/documentation/grape.html#_quick_start


## References

* [Jenkins Handbook, Chapter Pipeline](https://jenkins.io/doc/book/pipeline/) General description of a Jenkins pipeline, some basic but useful steps and common patterns, and Jenkinsfile examples.
* [Pipeline Steps Reference](https://jenkins.io/doc/pipeline/steps/)
