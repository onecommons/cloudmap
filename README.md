# Cloud Map: Blueprints for a Free and Open Cloud

A cloud map is a document that describes git repositories and the software, services, and applications that can be built from them. What makes a cloud map more than just a collection of metadata is that it uses a schema to express the dependencies between code repositories, artifacts, and applications. This schema follows the [TOSCA standard](#about-tosca) so that a cloud map can be used to automate the building and deployment of applications and services.

Currently this repository contains a [cloudmap.yaml](./cloudmap.yaml) file generated from the projects at <https://unfurl.cloud/onecommons/blueprints>. In the near future, we plan to automate this process so that anyone hosting a public repository on [Unfurl Cloud](https://unfurl.cloud) will have the option to have it added to this cloud map -- we want to make it as simple as possible for anyone to contribute to a free and open cloud.

## Motivation

Let's start with the grand vision: There's a vast universe of open-source projects and applications but it often takes a lot of work to integrate, configure, and deploy all these components into a useful running service. If there was a way to easily express those integrations in a composable, reusable manner that would go a long way towards building a [free and open cloud](https://www.unfurl.cloud/blog/the-free-and-open-cloud). Imagine a cloud map as the heart of something like a package manager for deploying web services and cloud infrastructure.

But our initial use-cases are a lot more modest:

* [Unfurl](https://github.com/onecommons/unfurl) uses cloud maps to discover cloud blueprints that match the requirements of its user's environment.
* Cloud maps are used to manage and synchronize repositories hosted by instances of Unfurl Cloud.
* [Unfurl](https://github.com/onecommons/unfurl) can also synchronize local git repositories and Gitlab projects with a cloud map.

## Anatomy of a cloud map

If you take a look at the [cloudmap.yaml](./cloudmap.yaml) in this repository, you'll find a YAML file with sections that describe the following:

### Git Repositories

The bulk of a cloud map file is in the `repositories` section, which consists of a list of metadata about git repositories, each of which looks like:

```yaml
repositories:
  unfurl.cloud/onecommons/blueprints/mediawiki:
    git: unfurl.cloud/onecommons/blueprints/mediawiki.git
    path: onecommons/blueprints/mediawiki
    name: MediaWiki
    protocols:
    - https
    - ssh
    internal_id: '35'
    project_url: https://unfurl.cloud/onecommons/blueprints/mediawiki
    metadata:
      description: MediaWiki is free and open-source wiki software used in Wikipedia,
        Wiktionary, and many other online encyclopedias.
      issues_url: https://unfurl.cloud/onecommons/blueprints/mediawiki/-/issues
      homepage_url: https://unfurl.cloud/onecommons/blueprints/mediawiki
    default_branch: main
    branches:
      main: 0fc60cb3afd06ae2c4abe038007e9ff4db398662
    tags:
      v1.0.0: 0fc60cb3afd06ae2c4abe038007e9ff4db398662
      v0.1.0: 225932bc2a45473d682e48f272bc48fcd83909bb
    notable:
      ensemble-template.yaml#spec/service_template:
        artifact_type: artifact.tosca.ServiceTemplate
        name: Mediawiki
        version: 0.1
        description: MediaWiki is free and open-source wiki software used in Wikipedia,
          Wiktionary, and many other online encyclopedias.
        type:
          name: Mediawiki@unfurl.cloud/onecommons/blueprints/mediawiki
          title: Mediawiki
          extends:
          - Mediawiki@unfurl.cloud/onecommons/blueprints/mediawiki
          - unfurl.nodes.SQLWebApp@unfurl.cloud/onecommons/std:generic_types
          - SQLWebApp@unfurl.cloud/onecommons/std:generic_types
          - WebApp@unfurl.cloud/onecommons/std:generic_types
          - _ContainerAppBase@unfurl.cloud/onecommons/std:generic_types
          - App@unfurl.cloud/onecommons/std:generic_types
          - tosca.nodes.Root
          - tosca.capabilities.Node
          - tosca.capabilities.Root
        dependencies:
        - MySQLDB@unfurl.cloud/onecommons/std:generic_types
        artifacts:
        - docker.io/bitnami/mediawiki
...
artifacts:
  docker.io/bitnami/mediawiki:
    type: artifacts.OCIImage
```

The `notable` section lists files and directories in the repository that are useful for characterizing the repository and integrating it with the other resources in the cloud map. Unfurl looks for files of interest when synchronizing repositories with the cloud map but entries can be added manually too. For example, `Dockerfiles` for building container images or `ensemble-template.yaml` containing cloud blueprints for deploying.

### Artifacts

Most code isn't used directly, instead there is a build or packaging process to create the the artifact (for example, an executable binary, a software package, or a container image) that actually used when an application is deployed; the `artifacts` section of a cloud map lists artifacts.

Artifacts are declared separately from repositories because there isn't necessarily a way to determine what artifacts are built and their location, but that relationship can be expressed with the `builds` annotation in the `notable` section. In the future, the cloud map schema will be extended to better represent the build processes build artifacts from code in repositories.

### Cloud blueprints

A cloud blueprint is a TOSCA service template that describes how to deploy a service or application includes a its dependencies, such as artifacts (e.g. container images) or other services (e.g. a database service).  Most of the repositories currently in this cloud map contain `cloud blueprints` -- you'll see a summary of the blueprint in the `notable` section of those repositories.

Blueprints are designed to be abstract enough to be used in a variety of environments. For example, a blueprint can specify a compute instance for a container image and that blueprint can deployed anywhere from a cheap virtual server to on a Kubernetes cluster.

## About TOSCA

[TOSCA](https://github.com/oasis-open/tosca-community-contributions) (Topology and Orchestration Specification for Cloud Applications) is the glue that ties the services in a cloud map together. TOSCA is a standard for describing and deploying cloud-provider independent abstractions of cloud infrastructure and services. We choose TOSCA because it is the only general purpose open standard for describing and orchestrating deployments -- isn't tied to a particular software ecosystem like Kubernetes, allowing a wide-range of abstractions to be expressed. A cloud map declares the TOSCA schema it uses to describe the entities it declares, such as artifacts, services, and processes. Using TOSCA allows a TOSCA processor like [Unfurl](https://github.com/onecommons/unfurl) the services found in the cloud map.

## More than an Awesome List

Blueprints can adapt themselves to different environments, based on its declared requirements. Environments are defined by the TOSCA schema the blueprints use but environments can be grouped like this:

* Cloud-providers like AWS or Google Cloud. The blueprint can run on and integrates with cloud-provider specific resources and APIs.

* Self-hosted: You can run the blueprint on your own machines or compute instances and integration is done by configuring those machines.

* Kubernetes / Cloud Native: The blueprint runs in a Kubernetes cluster and integrates with other services is done using cloud native APIs.

You can create master blueprints that connects together other blueprints -- one vision for the cloud map is enable users to cloud architecture blueprints for different environments and use-cases that connect best-of-breed blueprint applications and services.

## Adding to the Cloud Map

If you want to help build a free and open cloud or just make it easy an way for your users to deploy your application, its easy to contribute:

* Create blueprints for your favorite software using our [project templates](https://unfurl.cloud/projects/new#create_from_template) and automatically sync your blueprint with the cloud map.

* Help build out the [Unfurl Cloud standard library](https://onecommons.pages.staging.unfurl.cloud/std/) -- for example, by adding more and better cloud-provider agnostic abstractions.

* Adding your own repositories to the cloud map. If you have local clones of git repositories you want to add, one simple way to do that is with this command:

`unfurl cloudmap --import local --clone-root ../my-repos https://github.com/onecommons/cloudmap`

where `my-repos` is a local directory containing or more git repositories. This command will clone this repository, update the cloud map with metadata from those local repositories, and then create a branch with those changes. If you'd like to add your own repositories to this cloud map that would be awesome, just submit a pull request.

What would you like to see in a cloud map? Let us know! File an issue at <https://unfurl.cloud/onecommons/cloudmap/-/issues/new> or find us on [Discord](http://www.unfurl.cloud/discord).
