# JFrog Pipelines Experiments

JFrog Pipelines environment: [yinonatest1.jfrog.io](https://yinonatest1.jfrog.io)

This is a collection of (currently 1..) experiments with JFrog Pipelines.

## Experiments

1. [**On Artifact Deployed**](./on-artifact-deployed/)  
   An experiment with using JFrog Pipelines as an execution engine for handling JFrog Platform events.

## Prerequisites

There are several prerequisites for all the experiments.

1. **Default Node Pool**  
   Every JFrog Platform Cloud with Pipelines enabled comes with a default [node pool](https://www.jfrog.com/confluence/display/JFROG/Choosing+Node+Pools). 
   So there is nothing special to do here.

2. **GitHub Integration**  
   In order to use these experiments, first create a [GitHub Integration](https://www.jfrog.com/confluence/display/JFROG/GitHub+Integration), name it e.g. `my_github`.
   This integration needs to be provided with a token granting the needed access privileges.

3. **Pipeline Source**  
   In order to use the experiments in this git repository, you need to create a new [pipeline source](https://www.jfrog.com/confluence/display/JFROG/Managing+Pipeline+Sources) pointing to this repository (or your fork, if you forked it..).
   To create the pipeline source, use "From YAML":  
   | Configuration Field      | Value |
   | ------------------------ | ----- |
   | Branch Type              | Single branch |
   | Protocol Type            | `SSH` (preferred) |
   | Name                     | Give it a name, e.g. `github_jfrog_pipelines_experiments` |
   | SCM Provider Integration | Select the GitHub integration, e.g. `my_github` |
   | Repository Full Name     | For example: `yinonavraham/jfrog-pipelines-experiments` |
   | Branch                   | `main` |
   | Pipelines Config Filter  | Use: `(.*/)?pipelines(-.*)?.yml` |

