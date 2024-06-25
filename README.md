# Fermyon and SUSE Youtube live demo

This repo contains the different files and references used during a Youtube live session between [Fermyon](https://www.fermyon.com/) and [SUSE](https://www.suse.com/).

The topic was **Giving Rancher Desktop A Spin: Wasm on Kubernetes**

Youtube replay: https://www.youtube.com/live/EJxpxowmx6Y

*(Session occured on the 25th June 2024)*

## Prerequisites

The setup used during the demo was the following:

- Main OS: [OpenSUSE Aeon RC2](https://aeondesktop.org)
- Virtual Machine: [Windows Server 2025 Standard build 26236](https://techcommunity.microsoft.com/t5/windows-server-insiders/announcing-windows-server-preview-build-26236/m-p/4166605)
- Remote desktop: [MacOS Sonoma](https://www.apple.com/macos/sonoma/)
- [Rancher Desktop v1.14+](https://docs.rancherdesktop.io/getting-started/installation)

### Dependencies

The following applications were installed as dependencies for the demo:

- Rustup

| Linux | Windows | MacOS |
| ----- | ------- | ----- |
| sudo zypper install -y rustup | winget install rustup | brew install rustup |

- Git

| Linux | Windows | MacOS |
| ----- | ------- | ----- |
| sudo zypper install -y git-core | winget install git.git | brew install git |

- [Optional] Visual Studio Code

| Linux | Windows | MacOS |
| ----- | ------- | ----- |
|  | winget install  | brew install visual-studio-code |

- [Optional] Rest client plugin for Visual Studio Code

Click on the `Plugins` icon and search for "Rest Client" > Click Install

- Windows

Build tools need to be also installed for building the demos:

```powershell
winget install Microsoft.VisualStudio.2022.BuildTools
```

## Configuration

The following configurations were done on Rancher Desktop:

- Select `containerd` in `Preferences > Container Engine`
- Enable `WebAssembly` in `Preferences > Container Engine`
- Enable `Install Spin Operator` in `Preferences > Kubernetes`

### Rust WASI target

For the RUST examples, the following libraries were added:

```shell
rustup target add wasm32-wasi
```

## Demo

This demo showcased the different components available after a fresh install of Rancher Destkop.

### Docker / Nerdctl / Kubectl

Depending on the container runtime selected, `docker` or `nerdctl` will be the container management CLI available.

`kubectl` is available for both container runtimes if `Kubernetes` is enabled.

### Spin CLI

Spin CLI is available and enabled by default and can be used as a standalone with the `spin` command.

## Demo

This demo was based on the [SpinKube Quickstart](https://www.spinkube.dev/docs/spin-operator/quickstart/).

After the install and configuration of Rancher Desktop, the demo was the following:

```shell
# Install the app
kubectl apply -f https://raw.githubusercontent.com/spinkube/spin-operator/main/config/samples/simple.yaml

# Port forward
kubectl port-forward svc/simple-spinapp 8083:80

# Test it
curl localhost:8083/hello
```

### Ingress

An alternate way for accessing the app using the default Rancher Desktop ingress was also part of the demo:

```shell
# Create a YAML file for the ingress with the following content
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: spinapp
  namespace: default
  annotations:
    ingress.kubernetes.io/ssl-redirect: "false"

spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name:  simple-spinapp
            port:
              number: 80

# Apply the Ingress configuration
kubectl apply -f [ingress file]

# Test it
curl localhost/hello
```

### Clean-up

Once the demo showed, the following clean-up tasks were performed:

```shell
# Delete the Ingress configuration
kubectl delete -f [ingress file]

# Delete the app
kubectl delete -f https://raw.githubusercontent.com/spinkube/spin-operator/main/config/samples/simple.yaml
```

## Demo 

This demo was based on the blog [Introducing SpinKube](https://www.spinkube.dev/blog/2024/03/13/introducing-spinkube/).

```shell
# Create a new Spin App
spin new -t http-rust --accept-defaults spin-kube-app
cd spin-kube-app

# Build the Spin App
spin build

# Push the Spin App to an OCI registry
export IMAGE_NAME=ttl.sh/spin-app-$(uuidgen):1h
spin registry push $IMAGE_NAME
```

### Clean-up

Once the demo showed, the following clean-up tasks were performed:

```shell
# Delete app
spin kube scaffold -f $IMAGE_NAME | kubectl delete -f -
```

## Demo

This demo was based on the [Enterprise Architectures and Patterns - caching-rust](https://github.com/fermyon/enterprise-architectures-and-patterns/tree/main/caching-rust) which available on the Fermyon GitHub repo.

### Local

With all the dependencies installed as mentioned above, the demo was the following:

```shell
# Build the app
spin build

# Run the app and load the DB data
spin up --runtime-config-file ./local.toml --sqlite @migrations.sql

# Test it
```

### Cloud


### Kube - CHALLENGE

How can we deploy this demo to SpinKube?