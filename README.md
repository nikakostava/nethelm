# Kubernetes NetworkPolicy

This is a Kubernetes NetworkPolicy resource written in YAML. It allows you to control traffic to and from pods within your cluster by defining rules that specify what traffic is allowed or blocked.

## Getting Started

To use this NetworkPolicy in your Kubernetes cluster, you can follow these steps:

1. Copy the YAML code for this NetworkPolicy into a file called `networkpolicy.yaml`.
2. Replace the placeholders in the YAML code with the appropriate values for your use case.
3. Apply the NetworkPolicy to your cluster by running the following command: `kubectl apply -f networkpolicy.yaml`.

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


## YAML Code Explanation

Let's take a closer look at the YAML code for this NetworkPolicy and what each section does:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: {{ .Values.metadata.name }}
  namespace: {{ .Values.metadata.namespace | default "default" | quote }}
```
- `apiVersion`: specifies the Kubernetes API version used by this resource.
- `kind`: specifies the type of Kubernetes resource this is (in this case, a NetworkPolicy).
- `metadata`: provides information about the resource, such as its name and namespace.

```yaml
spec:
  podSelector:
    matchLabels:
      {{ .Values.spec.podselector.key }}: {{ .Values.spec.podselector.value }}
```
- `spec`: defines the policy specification for this NetworkPolicy.
- `podSelector`: selects the pods to which this policy applies.
- `matchLabels`: specifies the labels that the selected pods must have in order to be affected by this policy.

```yaml
  policyTypes:
  {{ if and (.Values.policytypes.in)  (.Values.policytypes.out) }}
    - {{ .Values.policytypes.in }}
    - {{ .Values.policytypes.out }}
  {{ else if .Values.policytypes.in }}
    - {{ .Values.policytypes.in }}
  {{ else if .Values.policytypes.out }}
    - {{ .Values.policytypes.out }}
  {{ end }}
```
- `policyTypes`: specifies the types of policies that this NetworkPolicy applies to.
- This section uses conditional statements to determine whether inbound and/or outbound policies are defined in the YAML code, and includes them accordingly.

```yaml
  ingress:
    - from:
    {{ if and (.Values.ingress.podselector.key) (.Values.ingress.podselector.value) 
    (.Values.ingress.namespaceselector.key) (.Values.ingress.namespaceselector.value) 
    (.Values.ingress.ipblock.cidr) (.Values.ingress.ipblock.except) }}
        - podSelector:
            matchLabels:
              {{ .Values.ingress.podselector.key }}: {{ .Values.ingress.podselector.value }}  
        - namespaceSelector: 
            matchLabels:
              {{ .Values.ingress.namespaceselector.key | default "kubernetes.io/metadata.name" | quote}}: {{ .Values.ingress.namespaceselector.value | default "default" | quote}}     
        - ipBlock:
            cidr: {{ .Values.ingress.ipblock.cidr }}
            except:
              - {{ .Values.ingress.ipblock.except }}
    {{ else if and (.Values.ingress.podselector.key) (.Values.ingress.podselector.value) 
    (.Values.ingress.namespaceselector.key) (.Values.ingress.namespaceselector.value) 
    (.Values.ingress.ipblock.cidr) }}
        - podSelector:
            matchLabels:
              {{ .Values.ingress.podselector }}