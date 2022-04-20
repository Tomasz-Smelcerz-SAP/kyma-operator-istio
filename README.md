# kyma-operator-istio

This project hosts two golang modules: one with the k8s API for the operator, the second with the operator itself.
The split allows for other projects to import just the API without having to import all the dependencies/libraries of the operator implementation.

steps:
1) Setup the repository

2) Create subdirectories for API and the controller
    mkdir k8s-api
    mkdir operator

3) Generate k8s-api project
    cd k8s-api
    go mod init
    kubebuilder init --domain kyma-project.io
Generate only the Resource, do NOT generate the Controller!
    kubebuilder create api --group kyma --version v1alpha1 --kind IstioConfiguration 
    make manifests
4) Push changes to github

5) Generate operator project
    cd operator
    go mod init
    kubebuilder init --domain kyma-project.io
Generate only the Controller, do NOT generate the Resource!
    kubebuilder create api --group kyma --version v1alpha1 --kind IstioConfiguration
    make build
6) Push changes

