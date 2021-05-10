##### Table of Contents  
* [Intention of Document](#intention-of-Document)
* [Building knowledge](#building-knowledge)
* [Developing the API](#developing-the-api)
* [Understanding CRDs](#understanding-CRDs)
* [Controllers and contexts](#controllers-and-contexts)
* [Writing Tests](#writing-tests)

# Intention of Document

**Intended audience:** developers new to working on harvester. This should be the next step after reading ["Setting Up Harvester Development Environment"](https://github.com/harvester/harvester/wiki/Setting-Up-Harvester-Development-Environment).

**Goal:** kickstart development by explaining commonly used concepts/components/constructs.

# Building knowledge

## Kubernetes

Starting with Borg, Omega, and Kubernetes https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/44843.pdf
Then you know everything you need should be at https://kubernetes.io/ , especially the following sections in https://kubernetes.io/docs/concepts/:

1. Overview
2. Containers
3. Workloads
4. Storage
5. Extending Kubernetes

## Go

A nice and easy to learn/use language. https://golang.org/doc/

Beware of channel.

## Git
We’re using git rebase instead of git merge. We’re using the following development flow:

1. git clone upstream master, so upstream is origin
1. git remote add personal fork of upstream repo as e.g. yasker
1. When starting development, checkout master branch, run git pull --rebase to pull any latest upstream master changes.
1. Then create a new branch using git checkout -b work to start your work.
1. From time to time, run git pull --rebase on master branch and git rebase masteron work branch to keep up with the upstream master changes.
1. When you’re ready to submit the PR, on work branch, use git push yasker work(remember yasker is your own personal repo) to create/update your own repo’s branch work
1. You may need git push -f. Be very careful when using git push -f though.
1. Go to GitHub, submit the PR.
1. After your PR is merged, update your local master as usual using git pull --rebase, then switch to work branch, run git rebase master to pull in the changes from upstream master.

No PR should have a merge commit if you follow the above flow.

# Developing the API

General API Docs: https://github.com/rancher/api-spec/blob/master/specification.md

You can inspect or interact with Harvester APIs using the **API UI** via `<Harvester-URL>/v1`.


## API Pathing

### Handling requests with formatters and stores
![](https://user-images.githubusercontent.com/5697937/117423665-8dbc3400-af53-11eb-9fc0-8de1e594e079.png)
Formatters and stores are used to handle requests. By using them, we can return errors, transform a request, and redact information from a response.

### Formatters

If you want to change the output for a schema in the API, you would probably want to use a formatter. A formatter is the only place where links can be redacted from.

### Action Handlers

Action handlers are used when the API needs to be capable of an action that is not: create, update, delete, get, watch. An example would be the 'backup' or 'restore' actions for VMs.

### Stores
Stores can transform and filter requests. Stores can be used for Delete, Watch, List, and ByID, in addition to Create and Update.


# Understanding CRDs

Much of the API works by forwarding requests to and from the Kubernetes API. Most objects in the API map to Kubernetes objects. Kubernetes comes with some of these objects out of the box. Others were added by creating CRDs. CRDs (custom resource definitions) are a means of extending the Kubernetes API by creating custom objects. Harvester creates CRDs by defining them in `pkg/apis`, then generating the types and clientsets using `pkg/codegen`, then instantiating them on [startup](https://github.com/harvester/harvester/blob/d01ee7b74a8f13a8702dc3b0e04ba8297c61ccbc/pkg/controller/crds/setup.go#L20).

Once a CRD has been added, objects of the CRD can be interacted with via kubectl or the Harvester API. These interactions can be one of 5 verbs: create, get, update, delete, watch. 

Reference: [Read more about CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/)


# Controllers and contexts


## Controller pattern

When users interact with resource objects via kubectl or the harvester API, we may want to perform some logic. This is done using controllers. A controller can add logic that will be executed whenever a resource object is created, removed, etc...

>In applications of robotics and automation, a control loop is a non-terminating loop that regulates the state of the system. In Kubernetes, a controller is a control loop that watches the shared state of the cluster through the API server and makes changes attempting to move the current state towards the desired state. Examples of controllers that ship with Kubernetes today are the replication controller, endpoints controller, namespace controller, and serviceaccounts controller.
>
>Kubernetes official documentation, [Kube-controller-manager](https://kubernetes.io/docs/admin/kube-controller-manager/)

A simple pseudocode of a controller:
```golang
for {
  desired := getDesiredState()
  current := getCurrentState()
  makeChanges(desired, current)
}
```

To add a controller in harvester, you implement the handler interface and call `OnChange` / `OnRemove` functions of the generated clients. [Example](https://github.com/harvester/harvester/blob/d01ee7b74a8f13a8702dc3b0e04ba8297c61ccbc/pkg/controller/master/node/promote_controller.go#L93)

## Contexts

Controllers in harvester run in one of two contexts, scaled and management. In an HA setup there are:
* **one** ScaledContext per **harvester server** (3)
* **one** ManagementContext per **harvester server master** (1)

## Number of controllers

In an HA setup with three Harvester servers we will run:

* One set of [management-controllers using the management context](https://github.com/harvester/harvester/blob/d01ee7b74a8f13a8702dc3b0e04ba8297c61ccbc/pkg/controller/master/setup.go#L65-L73) on currently elected leader of harvester servers
* One set of [controllers using the scaled context](https://github.com/harvester/harvester/blob/d01ee7b74a8f13a8702dc3b0e04ba8297c61ccbc/pkg/controller/global/setup.go#L30-L34) on every harvester server

**Additional resources:**

* [Kubernetes controllers](https://speakerdeck.com/thockin/kubernetes-controllers-are-they-loops-or-events)
* [A deep dive into Kubernetes controllers](https://engineering.bitnami.com/articles/a-deep-dive-into-kubernetes-controllers.html)
* [Writing Kubernetes Controllers for CRDs: Challenges, Approaches and Solutions - Alena Prokharchyk](https://www.youtube.com/watch?v=7wdUa4Ulwxg)
* [Extending Kubernetes 101](https://www.youtube.com/watch?v=yn04ERW0SbI)


# Writing Tests

There are three types of tests: unit, integration, and E2E.

## Unit Tests

Unit tests are written in go and handle code-level functionality such as testing single functions. They should have the a file name that ends with "_test.go", like "handler_test.go".


## Integration Tests

Integration tests are written in go using ginkgo. They mainly interact with the API the same way the UI would, in order to test high-level functionality. [See the readme for more info](https://github.com/harvester/harvester/blob/master/tests/README.md#harvester-testing). 

## E2E Tests

E2E tests are work in progress. 