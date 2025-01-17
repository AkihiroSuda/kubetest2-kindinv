# kubetest2 driver for Kubernetes in (Rootless) Docker in (GCE) VM

`kubetest2-kindinv` provides a "kind in VM" driver for [kubetest2](https://github.com/kubernetes-sigs/kubetest2).

This driver was written for the sake of running the tests with [rootless kind](https://kind.sigs.k8s.io/docs/user/rootless/).


## Requirements
- [kubetest2](https://github.com/kubernetes-sigs/kubetest2).

- Docker and [`kind`](https://kind.sigs.k8s.io/) have to be installed on the local host,
  as the driver executes `kind build node-image` on the local host (currently).
  The local host can be macOS.

- `gcloud` command has to be configured with the permissions for creating and removing the following resources:
  - GCE Instances
  - VPCs
  - Firewall rules

## Usage

```bash
go install github.com/rootless-containers/kubetest2-kindinv@master

cd ${GOPATH}/src/github.com/kubernetes/kubernetes
make WHAT=test/e2e/e2e.test
make ginkgo
make kubectl

kubetest2 kindinv \
  --run-id=foo \
  --gcp-project=${CLOUDSDK_CORE_PROJECT} \
  --gcp-zone=us-west1-a \
  --instance-image=ubuntu-os-cloud/ubuntu-2204-lts \
  --instance-type=n2-standard-4 \
  --kind-rootless \
  --kube-root=${GOPATH}/src/github.com/kubernetes/kubernetes \
  --build \
  --up \
  --down \
  --test=ginkgo \
  -- \
  --use-built-binaries \
  --focus-regex='\[NodeConformance\]' \
  --skip-regex='Sysctl .*|\[Slow\]' \
  --parallel=8
```

The example command above usually takes more than 30 minutes in total.
