# kyma-operator-istio

POC for the repository layout of the operator-based Kyma reconciliation project.

This repository hosts a sample operator. This is a dummy operator, the name (Istio) is chosen just to be familiar for people knowing Kyma.

There are two golang modules here: one for the public k8s API of the operator, the second one with the operator implementation.
The split allows for other projects to import just the API without having to import all the dependencies/libraries of the operator implementation.
This repository layout allows for building the project using Kubebuilder with just minimum amount of manual "patching".

This is because Kubebuilder [does not support such model yet](https://github.com/kubernetes-sigs/kubebuilder/blob/master/docs/using_an_external_type.md), but also does not "block" it.


The Steps to setup the entire thing:

1) Setup the github repository

2) Create subdirectories for API and the controller

    `mkdir k8s-api`
    
    `mkdir operator`

3) Generate k8s-api project

    `cd k8s-api`

    `go mod init`

    `kubebuilder init --domain kyma-project.io`
    
    **Note: Generate only the Resource, do NOT generate the Controller!**

   `kubebuilder create api --group kyma --version v1alpha1 --kind IstioConfiguration`

   `make manifests`

4) Push changes to github

5) Generate operator project

    `cd operator`

    `go mod init`

    `kubebuilder init --domain kyma-project.io`

    **Note: Generate only the Controller, do NOT generate the Resource!**

    `kubebuilder create api --group kyma --version v1alpha1 --kind IstioConfiguration`

    `make build`

6) Push changes

7) Wire the `k8s-api` types to the operator project

    `cd operator`

    `go get -d github.com/Tomasz-Smelcerz-SAP/kyma-operator-istio/k8s-api`

  - Adjust `main.go` to register `k8s-api` types in the runtime Schema

  - Adjust `controllers/istioconfiguration_controller.go` so that it reconciles instances of IstioConfiguration type

8) Build and Test

- start the cluster (e.g: k3d cluster create kyma)
- `cd k8s-api`
- `make install` (installs CRDs)
- prepare sample file in `config/samples/kyma_v1alpha1_istioconfiguration.yaml`
- `cd ../operator`
- `make run`
- apply the sample file from `k8s-api/config/samples/kyma_v1alpha1_istioconfiguration.yaml`
- observe how the controller reacts to the creation and deletion of the sample CR instance

