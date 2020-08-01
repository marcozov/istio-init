# Istio installation

This repo allows to install Istio through standard Kubernetes files.

This installation is customized to deploy a private load balancer in Azure, in order to expose services in a private network.
It also exports few telemetry services, like Grafana and Kiali.

The demo profile was used to generate these installation manifests, but it can be easily adapted to use other profiles.

## Generation steps

They were generated with the following steps:

 - Dump an Istio specification: `istioctl profile dump demo > demo.yaml`.
 - Create the corresponding standard Kubernetes yaml manifest: `istioctl manifest generate -f demo.yaml > custom.yaml`.
 - Adapt `custom.yaml` to suit the environment needs:
    - Add the annotation `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` to the `istio-ingressgateway` Service.
    - Add port(s) to expose additional services through the ingress gateway. In this case, `15029` and `15031` were added for Kiali and Grafana, respectively.
    - Move the resources defined through CDR (EnvoyFilter and PeerAuthentication in this case) to another file (`1-istio-cdr.yaml`). All the rest has been moved to `0-istio-base.yaml`.
    - Add the `Gateway` and `VirtualService` resources in order to expose the services (`2-istio-ingressgateway-expose.yaml`).
