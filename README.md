# How To Set Up an Nginx Ingress on DigitalOcean Kubernetes Using Helm



*The author selected the [Free and Open Source Fund](https://www.brightfunds.org/funds/foss-nonprofits) to receive a donation as part of the [Write for DOnations](https://do.co/w4do-cta) program.*

### Introduction

Kubernetes [Ingresses](https://kubernetes.io/docs/concepts/services-networking/ingress/) offer you a flexible way of routing traffic from beyond your cluster to internal Kubernetes Services. Ingress *Resources* are objects in Kubernetes that define rules for routing HTTP and HTTPS traffic to Services. For these to work, an Ingress *Controller* must be present; its role is to implement the rules by accepting traffic (most likely via a Load Balancer) and routing it to the appropriate Services. Most Ingress Controllers use only one global Load Balancer for all Ingresses, which is more efficient than creating a Load Balancer per every Service you wish to expose.

[Helm](https://helm.sh/) is a package manager for managing Kubernetes. Using Helm Charts with your Kubernetes provides configurability and lifecycle management to update, rollback, and delete a Kubernetes application.

In this guide, you’ll set up the Kubernetes-maintained [Nginx Ingress Controller](https://github.com/kubernetes/ingress-nginx) using Helm. You’ll then create an Ingress Resource to route traffic from your domains to example Hello World back-end services. Once you’ve set up the Ingress, you’ll install [Cert-Manager](https://github.com/jetstack/cert-manager) to your cluster to be able to automatically provision Let’s Encrypt TLS certificates to secure your Ingresses.

## Prerequisites

- A DigitalOcean Kubernetes cluster with your connection configuration configured as the`kubectl` default. Instructions on how to configure `kubectl` are shown under the **Connect to your Cluster** step shown when you create your cluster. To learn how to create a Kubernetes cluster on DigitalOcean, see [Kubernetes Quickstart](https://www.digitalocean.com/docs/kubernetes/quickstart/).
- The Helm package manager installed on your local machine, and Tiller installed on your cluster. Complete steps 1 and 2 of the [How To Install Software on Kubernetes Clusters with the Helm Package Manager](https://www.digitalocean.com/community/tutorials/how-to-install-software-on-kubernetes-clusters-with-the-helm-package-manager) tutorial.
- A fully registered domain name with two available A records. This tutorial will use`hw1.your_domain` and `hw2.your_domain` throughout. You can purchase a domain name on[Namecheap](https://www.namecheap.com/), get one for free on [Freenom](https://www.freenom.com/en/index.html?lang=en), or use the domain registrar of your choice.

## Step 1 — Setting Up Hello World Deployments

In this section, before you deploy the Nginx Ingress, you will deploy a Hello World app called [`hello-kubernetes`](https://hub.docker.com/r/paulbouwer/hello-kubernetes/) to have some Services to which you’ll route the traffic. To confirm that the Nginx Ingress works properly in the next steps, you’ll deploy it twice, each time with a different welcome message that will be shown when you access it from your browser.

You’ll store the deployment configuration on your local machine. The first deployment configuration will be in a file named `hello-kubernetes-first.yaml`. Create it using a text editor:
