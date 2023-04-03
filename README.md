# Cloud Map: Blueprints for a Free and Open Cloud

This repository contains a [cloudmap.yaml](./cloudmap.yaml) file generated from the projects at https://unfurl.cloud/onecommons/blueprints.

The goal is for this repository to serve as a proof-of-concept for a cloudmap of a free and open cloud. A cloud map is catalog of open cloud services that describes how to build and deploy those services from source code. It will also describe the dependencies between services and lists service instances that are publically available. The information saved in the cloudmap is pretty minimal right now but that will change soon.

If you'd like to add your own repositories to this cloud map that would be awesome, just submit a pull request. If you have local clones of git repositories you want to add, one simple way to do that is with this command:

`unfurl cloudmap --sync local --clone-root ../my-repos https://github.com/onecommons/cloudmap`

where `my-repos` is a local directory containing or more git repositories.
