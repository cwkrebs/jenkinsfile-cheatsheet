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


## References

* [Jenkins Handbook, Chapter Pipeline](https://jenkins.io/doc/book/pipeline/) General description of a Jenkins pipeline, some basic but useful steps and common patterns, and Jenkinsfile examples.

