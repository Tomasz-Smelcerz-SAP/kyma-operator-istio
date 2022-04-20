# kyma-operator-istio

steps:
1) Setup the repository

2) Create subdirectories for API and the controller
    mkdir k8s-api
    mkdir operator

3) Generate k8s-api project
    cd k8s-api
    go mod init
    kubebuilder init --domain kyma-project.io
    kubebuilder create api --group kyma --version v1alpha1 --kind IstioConfiguration
    make manifests
4) Push changes to github

