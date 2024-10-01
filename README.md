# Overview
This is a repo demoing DevZero.

## Demo 1 - Quickstart Use Case - Starting with a Recipe and a Workspace


## Demo 2 - Enhancing Microservices Development with DevZero

This demo is based on this tutorial below:
https://www.devzero.io/docs/workspaces/kubernetes-cluster#tutorial-steps

### Objective
Explore how DevZero supports teams building on microservices.

### Key Points
- Overview of the challenges in microservices development.
- Features of DevZero that facilitate upstream and downstream connections.
- Demonstration of networking magic in DevZero to connect various microservices components.

### Use Case
Demonstrate how teams building on microservices can benefit from DevZero’s advanced networking capabilities, ensuring seamless integration and communication between services​.

### A few commands

To get the workspace name
```bash
dz workspace ls
```

To write the config to the default Kubernetes configuration location, run
```bash
dz workspace kubeconfig <workspace-name> --update-kubeconfig
```


## Demo 3