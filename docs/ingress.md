## What is Ingress

Ingress is a way to route traffic from the internet to services within your Kubernetes cluster,
without creating a load-balancer for each service. For more information, review the
[Kubernetes Ingress Documentation][Kubernetes Ingress Documentation]

## Installing an Ingress Controller

### Cloud providers

While there are many ingress controllers available within the Kubernetes Community, for
simplicity, this guide will use the nginx-ingress from the stable helm charts, but you are
welcome to use any ingress controller.

These documents assume you are connected to a Kubernetes cluster running in a cloud-provider.

```shell
$ helm install stable/nginx-ingress --namespace=kube-system --name=nginx-ingress
```

After you've installed the nginx-ingress controller, wait for a Load Balancer to be created with:

```shell
$ kubectl --namespace kube-system get services -w nginx-ingress-nginx-ingress-controller
```

### Minikube

On minikube, you can simply enable the ingress controller addon

```shell
$ minikube addon enable ingress
```

The ingress IP addres is minikube's IP:

```shell
$ minikube ip
```


## Point a wildcard domain

Draft uses a wildcard domain to make accessing draft-created applications easier. To do
so, it specifies a custom host in the ingress from which tells the backing load balancer
to route requests based on the Host header.

Using a domain that you manage, create a DNS wildcard `A Record` pointing to the ingress IP address.

**NOTE:** you are welcome to use `*.draft.example.com` or any other wildcard domain.

Remember the domain you use, it will be needed in the next step of installation as the `basedomain` passed to `draft init`.

| Name          | Type | Data                      |
|---------------|------|---------------------------|
| *.example.com | A    | `<ip address from above>` |


### I don't manage a domain

If you don't manage a domain you can't directly use the domain in your request.
To fulfill the load balancer request to have the Host header provided, you can
explicitly provide it:

```
$ curl --header Host:<application domain> <ip address from above>
```

You could also edit your `/etc/hosts` file to point
to the ingressed out application domain to your cluster.

The following snippet would allow you to access an application:

```
$ sudo echo <ip address from above> <application domain> >> /etc/hosts
```

The draw back is that `/etc/hosts` does not support wildcards, so you would
need to add an entry for each application deployed by Draft. For wildcard
support you can use `dnsmasq`. Refer to `dnsmasq` documentation for your
platform.

Some sources of information:
 * [Arch Linux dnsmasq]( https://wiki.archlinux.org/index.php/dnsmasq)


## Next steps

Once you have an ingress controller installed and configured on your cluster,
you're ready to install Draft.

Continue with the [Installation Guide][Installation Guide]!


[Installation Guide]: install.md#install-draft
[Kubernetes Ingress Documentation]: https://kubernetes.io/docs/concepts/services-networking/ingress/
