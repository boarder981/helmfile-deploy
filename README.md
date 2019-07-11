# helmfile-deploy

This project provides a framework of *helmfiles* using the amazing tool [helmfile](https://github.com/roboll/helmfile) for declaring intended Kubernetes state for apps deployed with the [appdeploy](https://github.com/bitsofinfo/appdeploy) Helm chart and accessed via Ingress "conduits" installed by the [appconduits](https://github.com/bitsofinfo/appconduits) Helm chart.

*helmfile-deploy* is a opinionated *framework* (for lack of a better term) that provides a set of *helmfiles* that generate *releases* for the [appdeploy](https://github.com/bitsofinfo/appdeploy) and [appconduits](https://github.com/bitsofinfo/appconduits) charts. To generate those releases, *helmfile-deploy* defines its own YAML syntax for summarizing the desired state for applications it manages. This desired "state" is expressed as *helmfile* `environments:`, one per named target application.

* [Overview](#overview)
* [helmfile terminology](#helmfile-term)
* [helmfile-deploy terminology](#helmfile-dep-term)

## <a id="overview"></a>Overview

What does `helmfile-deploy` do? How does it work?

Let's cover some basics and get on the same page w/ our terminology.

### <a id="helmfile-term"></a>Helmfile specific terminology

First, some basic *helmfile* terminology you need to understand:

(for full documentation on this see: https://github.com/roboll/helmfile)

**helmfile**:  
A *helmfile* is a YAML file that declares one or more *releases* for Helm charts. You use the [helmfile](https://github.com/roboll/helmfile) command against a target *helmfile yaml file*. Helmfiles themselves can contain [golang template](https://golang.org/pkg/text/template/) markup as well as utilize many [extended functions provided by Sprig.](http://masterminds.github.io/sprig/)

**release**:  
A Helmfile *release* is an expression in YAML for an installation/upgrade of a specific Helm chart and its desired `values`. With [helmfile](https://github.com/roboll/helmfile), the goal is to be able to define many *releases* and then ensure all or some of the defined *releases* are applied against a target Kubernetes cluster.

**state values**:  
Helmfile *state values* are variables/values that are only relevant during the execution of `helmfile` while it is processing a target *helmfile yaml*. *State values* are distinctly different than Helm chart values. *State values* can be referenced in helmfiles in golang templates to dynamically generate helmfile *releases*.

**environment values**:  
When you invoke the `helmfile` command against a target *helmfile yaml file*, you also specify a target `--environment [name]`. Doing so will instruct *helmfile* to load a set of *environment values* specific to that target `--environment`. These values (in combination with *state values*) can be used as variables to also drive the generation of *releases* in the target *helmfile yaml file* that the `helmfile` command will process. It is important to NOT confuse *helmfile environment values* with traditional OS ENVIRONMENT variables... they are NOT the same thing. It is also important to note that a target *environment* can mean *anything* and does NOT necessarily have to mean something like "stage" or "production", but could simply be a target *application* to generate a release for etc.

### <a id="helmfile-dep-term"></a>helmfile-deploy specific terminology

... and some basic *helmfile-deploy* terminology you need to understand:

**appdeploy and appconduits helm charts**:    
To even begin using *helmfile-deploy* you first need to be familiar with the concepts and functionality of the [appdeploy](https://github.com/bitsofinfo/appdeploy) and [appconduits](https://github.com/bitsofinfo/appconduits) Helm charts. If you have not already done so, now would be a good time to review them and run through their examples:
* https://github.com/bitsofinfo/appdeploy
* https://github.com/bitsofinfo/appconduits

**environments**:  
When using *helmfile-deploy*, each helmfile *environment* that you target with `--environment [name]` is considered to be a named **"application"** you want to both deploy with [the appdeploy chart](https://github.com/bitsofinfo/appdeploy) and manage customized Ingress routes (conduits) for with [the appconduits chart](https://github.com/bitsofinfo/appconduits). With *helmfile-deploy* each *target application* IS expressed through a *helmfile environment*. (*helmfile environments* = *application desired state definitions*)

**applications**:  
With *helmfile-deploy* each *target application* IS expressed through a *helmfile environment*. To configure a new *application* that *helmfile-deploy* can manage, you define a new helmfile *environment* and express it's desired state in a custom YAML syntax that lets you define an `appname`, its app `environments`, `contexts` and most importantly `services` and `ingresses` within each app's environment/context. For definitions of app *environments/contexts* [see the appdeploy chart README](https://github.com/bitsofinfo/appdeploy)



### What does helmfile-deploy provide?

**helmfiles**
Provides two types of [helmfile](https://github.com/roboll/helmfile) *helmfiles*:
* [deployments.helmfile.yaml](deployments.helmfile.yaml): Generates a unique helmfile **release** of the [appdeploy Helm chart](https://github.com/bitsofinfo/appdeploy) for each declared `service` within a target helmfile `environment`
* [conduits.helmfile.yaml](conduits.helmfile.yaml): Generates a unique helmfile **release** of the [appconduits Helm chart](https://github.com/bitsofinfo/appdeploy) for each declared `service` within a target helmfile `environment`
