# Network Policy Helm Chart

This is a Helm chart for Kubernetes network policies. The chart contains a sample network policy that you can use as a starting point for creating your own policies.

## Prerequisites

* A Kubernetes cluster with version 1.19 or later
* Helm 3 or later installed on your local machine

## Installing the Chart

To install the chart, run the following command:

```bash
helm install network-policy ./network-policy
```

This command installs the chart with the release name `network-policy`.

## Customizing the Chart

You can customize the chart by modifying the values in the `values.yaml` file. The following table lists the configurable parameters and their default values:

| Parameter | Description | Default |
| --- | --- | --- |
| `metadata.name` | Name of the network policy | `network-policy` |
| `metadata.namespace` | Namespace where the network policy should be created | `"default"` |
| `spec.podselector.key` | Key of the pod selector | `"app"` |
| `spec.podselector.value` | Value of the pod selector | `"myapp"` |
| `policytypes.in` | Whether to allow incoming traffic | `true` |
| `policytypes.out` | Whether to allow outgoing traffic | `true` |
| `ingress.podselector.key` | Key of the ingress pod selector | `"app"` |
| `ingress.podselector.value` | Value of the ingress pod selector | `"myapp"` |
| `ingress.namespaceselector.key` | Key of the ingress namespace selector | `"app"` |
| `ingress.namespaceselector.value` | Value of the ingress namespace selector | `"myapp"` |
| `ingress.ipblock.cidr` | IP address block to allow traffic from | `"10.0.0.0/16"` |
| `ingress.ipblock.except` | IP addresses or blocks to exclude from the IP address block | `"10.0.0.1/32"` |

## Uninstalling the Chart

To uninstall the chart, run the following command:

```bash
helm uninstall network-policy
```

This command removes the chart with the release name `network-policy`.