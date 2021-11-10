*********************DECLARATIVE PIPELINES***********************************************************

1) In scripted Pipelines we have nodes and under that we have stages, 
2) In case of declarative pipelines we have syntax which starts with pipeline {}
pipeline{
  agent any
      stages{
          stage("Welcome"){
              steps{
                  echo "Welcome to declarative pipelines"
              }
          }
      }

  }


3) Global variables path --> http://localhost:8080/job/1-declarative-demo/pipeline-syntax/globals

4) we can use the Following to generate syntax in jenkins pipelines -> http://localhost:8080/job/1-git-checkout/pipeline-syntax/

5) environment block added is used to provide env variables to all the stages of that pipeline , or can also be added in a paticular stage 

********STEPS********** 
6) We need to write inside stage Directive
7) One steps directive should be there inside stage directive 
8) Contains steps (commands/scripts) that we used in the build

********STAGE**********
9) Defines paticular stage (build/test/deploy) of our job
10) Atleast one stage has to be there and the name we specify appears in the Jenkins Dashboard

*********STAGES*******
11) Contains sequence of stages and atleast one stage has to be there

**********AGENT*******
12) Where we need to run the pipeline script (master/slave/container)


*********************************************
13) echo is a print statement in Jenkins


***********************VARIABLES**********************************************************************

1) Types of variables are -> Pre-defined variables and user defined variables 
SYNTAX -->>   <VariableName> = "<VariableValue>"

2) Pre-defined variables are the ones given by jenkins by default 
http://localhost:8080/env-vars.html

3) Variable declaration can be done at 3 places :
   - Global (Pipeline) level
   - Stage level
   - Script level

* For defining variables under steps section, we can't use environment block,
We need to call it as scripts,
* Syntax is as follows -> 
steps{
                script{
                    ScriptName="Harkirat is awesome"
                }
                echo "Step defined variable is $ScriptName"
            }
4) Scope of variables 
If we define the same variable at global(pipeline) level and stage level, then the importance 
would be taken at stage level, not at pipeline level
5) [NOTE]
Preferance to variables -> Global < Stage < Script

*****Printing one previous value***************
we fetch the variable using the value as -->>> $(env.VariableName)
Generally user defined variable is present inside the Stages scope

pipeline{
    agent any
    environment{
        ToolName="Pipeline"
    }
    stages{
        stage("Pre and User defined variable scope"){
            environment{
                ToolName="Stage"
            }
            steps{
                script{
                    ToolName="Step"
                }
                echo "Variable taking precedance should be defined at step level - answer is $ToolName"
                echo "Variable defined one level back - user defined is ${env.ToolName}"
                
            }
        }
    }
}

[OUTPUT]

Variable taking precedance should be defined at step level - answer is Step
Variable defined one level back - user defined is Stage

Note: IF we remove the value at stage level, it will take previous value (For user defined variables)

6) If user defined variable we define a Paticular value, than it will take preferance

7) Single and Double Quotes Differance
IF we give any variable inside single quotes ->> 'Harkirat system is at location $PWD'
Output will be same -> Harkirat system is at location $PWD
But in double quotes output will be -> Harkirat system is at location /home/Harkirat30/Desktop

8) Concatenation Operator

* purpose is to combine 2 or more variables into a single string, 
* In jenkins the Concantenation [Operator is +]

pipeline{
    agent any
    environment{
        name="Harkirat"
        lastname="Singh"
    }
    stages{
        stage("Concatenation operator"){
            steps{
                script{
                    quote= name +" " +"is" + " " + lastname
                }
                echo "Using concatenation operator we print $quote"
                echo "Or other way around is using \$name is \$lastname -->>> $name is $lastname"
            }
            }
        }
    }

OUTPUT IS
Using concatenation operator we print Harkirat is Singh
Or other way around is using $name is $lastname -->>> Harkirat is Singh

***************************PARAMETERS***************************

1) Parameters are used to pass Data Dynamically 
2) Types of parameters -->
 * String
 * Text    ---> Used to pass in multiple lines which we can't do with String parameter 
 * Boolean
 * Choice  ---> Selecting value from Dropdown list
 * Password --> We won't be able to see the text that we are passing
 * File --> used when we want to upload some file during a build
 * Dry Run

3) Uses of Parameters 
They are used to Pass the values Dynamically unlike Variables which hold static data inside Jenkinsfile
4) Some logic is always **tested under script section** 
5) Under script section if we are doing some logic we need to use ${parms.VALUE}

NOTE: Since we configure job first time - if we don't update the choices the build will get failed, 
so to avoid that we can incorporate Dry run 

We need to add the below code in the first step -->>>

// In Dashboard you will see that the job has been ABORTED and not failed
// Now you can run the job using build paramater as No
// Now whenever you update the Jenkins job and doesn't want the job to fail, 
//just build the job will the Dry run variable as true

# the configuration file doesn't get updated when we run a Parametrise job, so we Build gets failed and then you run it again.
# this can be avoided by passing in the dry run parameter and adding code for that


****************************OPTIONS SECTION****************************
1) There are below types of options available-->>
  * retry -> before failing the job, will run the job again for a specified no of times 
  * buildDiscarder -> It is used to delete old build logs, generally used to save space 
  * disableconcurentbuild -> used to disable concurrent builds 
  * timeout -> If the job is not completed in a paticular timeperiod, it should mark it as fail 
  * timestamps -> We will come to know at what time our steps were executed 

**RETRY CODE** 

pipeline{
    agent any
    options{
        retry(3)
    }
    stages{
        stage("Checking retry"){
            steps{
                echo "Here some connectivity code is there that might fail due to connection error, so we will retry things here until specified time"
            }
            
    }
}
}

**buildDiscarder**

pipeline{
    agent any
    options{
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
    }
    stages{
        stage("Checking retry"){
            steps{
                echo "Ensuring it should only keep 5 builds"
            }
    }
}
}

**disableConcurrentBuilds**

Similarly as above under Options you pass in 
options{
    disableConcurrentBuilds()
}

**timeouts**

options {
      timeout(time: 1, unit: 'HOURS' or 'SECONDS' or 'MINUTES')
  }
**timestamps**

options {
      timestamps()
  }

****************************TRIGGER BUILDS****************************

Build triggers help us in following things->
1) Trigger builds from Pipeline script
2) How to trigger second build, even if first build fails
3) How to change build results 
4) Call a job by passing Parameters

**Trigger other builds(jobs) from Pipeline script**
* We use build('jobname') option

NOTE- How to trigger second job, even if first job fails 
--> If one of the job fails and the other does not get triggered - we will get following output -> 
# 1-kubectl-test #9 completed with status FAILURE (propagate: false to ignore)

# so we need to use the following syntax -> build(job:'jobname', propagate:false)

-->> With propogate as false, if one job fails and the last one passes the job status will be pass,
which is incorrect so we need to change that 

**How to change build result if one job passes and other fails in that respective job**

We need to use a function called result 

jobresult = build(job: "jobname", propogate:false).result

# variable declaration should always happen in Script section 
CODE 

pipeline{
    agent any
    options{
        timestamps()
    }
    stages{
        stage("Build other job"){
            steps{
                script{
                    jobresult= build(job:'1-kubectl-test', propagate:false).result
                    build '2-userdefined-variables'
                    if(jobresult=="FAILURE"){
                        currentBuild.result="UNSTABLE"
                    }
                // by default status of 2nd job will be status of the pipeline
                }
                
                
            }
        }
    }
}

Jenkins ouptut -->> 
Started by user Harkirat Singh 
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/jenkins_home/workspace/15-Calling-2-builds-and-updating-job-result-on-job-status
[Pipeline] {
[Pipeline] timestamps
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Build other job)
[Pipeline] script
[Pipeline] {
[Pipeline] build (Building 1-kubectl-test)
23:47:26  Scheduling project: 1-kubectl-test
23:47:36  Starting building: 1-kubectl-test #11
[Pipeline] build (Building 2-userdefined-variables)
23:47:41  Scheduling project: 2-userdefined-variables
23:47:51  Starting building: 2-userdefined-variables #3
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // timestamps
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: UNSTABLE



**Trigger other jobs with parameters**

Triggering downstream job from upstream job syntax.

