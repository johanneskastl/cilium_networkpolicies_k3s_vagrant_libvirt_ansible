# cilium_networkpolicies_k3s_vagrant_libvirt_ansible

Vagrant-libvirt setup that creates a VM with [k3s](https://k3s.io) and
[Cilium](https://cilium.io) as the CNI.

Cilium is also used as the standard ingress controller, i.e. Traefik is not used
in this scenario.

Ansible installs two instances of Nginx and exposes both of them using an
Ingress. The URLs of both ingresses will be shown at the end of the
provisioning.

You should be able to reach the `nginx-allowed` instance successfully.

However, the connection to `nginx-denied.192.0.2.13.sslip.io` (or whatever your
VM's IP will be) is denied by the NetworkPolicy that denies all access to the
`nginx-denied` namespace.

The `main` branch uses Cilium's own `CiliumNetworkPolicy` resources, while the
`kubernetes` branch uses the vanilla Kubernetes `NetworkPolicy` resources.

The branch `gateway_api` uses Cilium's GatewayAPI implementation instead of the
Kubernetes Ingress, i.e. there is no ingress controller running. Instead of
`Ingress` resources, `Gateway` and `HTTPRoute` resources are being used.

Default OS is openSUSE Leap 15.6, but that can be changed in the Vagrantfile.
Please be aware, that this might break the Ansible provisioning.

## Vagrant

1. You need `vagrant`, obviously. And `git`. And Ansible...
1. Fetch the box, per default this is `opensuse/Leap-15.6.x86_64`, using
   `vagrant box add opensuse/Leap-15.6.x86_64`.
1. Make sure the git submodules are fully working by issuing
   `git submodule init && git submodule update`
1. Run `vagrant up`
1. Run e.g. `curl` against both of the ingress URLs, which will fail on the
   `nginx-denied` one.
1. Party!

## Cleaning up

The VMs can be torn down after playing around using `vagrant destroy`. This will
also remove the kubeconfig file `ansible/k3s-kubeconfig`.
