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
http://localhost:8080/env-vars.html/