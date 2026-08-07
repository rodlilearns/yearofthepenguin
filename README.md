# yearofthepenguin
Cloud-native Linux VDI powered by OpenShift Virtualization. Making the "Year of the Linux Desktop" an enterprise reality.

## Mission Statement
For the vast majority of users both personal and enterprise, a daily workstation is simply a vehicle to access a web browser and check emails, write documents, and conduct research through a search engine.  
Yet, individuals and organisations continue to pour astronomical budgets into OS licensing, complex client management infrastructure, and privacy compromising telemetry for workloads that are fundamentally non-critical.  
`yearofthepenguin` exists to break that status quo.  
By leveraging OpenShift Virtualization, this project provides a lean, automated, and cloud-native pathway for the enterprise to transition routine desktop workloads to Linux.  
We treat virtual desktops as disposable, low-cost, containerised resources, eliminating bloated per-seat OS licenses, reclaiming data privacy, and lowering cost per seat.  
It's time to stop paying premium prices for basic computing and unnecessary software bloat.  
`yearofthepenguin` turns the "Year of the Linux Desktop" from a running joke into a pragmatic enterprise balance sheet strategy.  

## Requirements
Requirements are dynamically listed and prioritised with the MoSCoW framework.
1. The solution must be open source (no restrictions on the ability to read, write, edit, share source code).
2. The solution must allow users to use the web browser and achieve external network connectivity.
3. The solution must have an active community contributing to its upstream components.
4. The solution should use best practices for security.
5. The solution should have no licensing or subscription costs.
6. The solution should contribute to efficiency of the organisation's operations.
7. The solution should contribute to the stability of the organisation's I.T. infrastructure.
8. The solution should scale with diminishing effort and increasing returns.
9. The solution should be easy to audit.

## Architectural Concepts and Design Decisions  

### 1. Ephemeral First, Persistent when Necessary  
* Stateless Desktop Pools: Desktops are deployed as stateless, disposable VMs.  
* Persistent User Profiles: User data and configurations are decoupled from the OS disk and mounted dynamically.  
* Dynamic resource allocation based on real-time metric triggers (active sessions, authentication queues, time-of-day schedules)
* Warm Pool Buffer Management: Keep a margin of pre-initialised unallocated desktop instances in memory to eliminate login latency during peak traffic spikes.
  
Reason: Efficiency and stability in user experience by ensuring linear performance scaling while reducing idle compute costs during off-peak hours.  
Requirements: [6,7,8]  
  
### 2. GitOps Native Management  
* All desktop definitions, image sources, and network policies are defined declaratively.  
* Fleet management, updates, and scaling are handled through GitOps.  
  
Reason: Reliability, repeatability, scalability.  
Requirements: [7,8]   
  
### 3. Containerised Base Images  
* Base OS images are packaged as OCI-compliant `ContainerDisk` images stored in an image registry.  
* Updates to the desktop OS follow standard CI/CD container build pipelines.  
  
Reason: Auditability, consistency, scalability.  
Requirements: [7,8,9]  
  
### 4. Zero-Client Remote Protocol Proxying  
* Sessions are delivered via web browsers using HTML5 client streaming proxies.  
* Users access desktops over secure HTTPS via standard OpenShift Routes.  
  
Reason: More stable user experience by reducing dependencies.  
Requirements: [7]

### 5. Immutable Infrastructure via Just-in-Time Provisioning
* Operating system upgrades, patches, configuration changes are implemented through base container images rather than live Virtual Machines.
* Rolling shred-and-replace lifecycle, triggering cold-boot instantiations from the latest verified base image.

Reason: Eliminated configuration drift, prevents persistent malware infection, simplifies system auditing to single image digests.  
Requirements: [4,6,7,9]  

  
## Solution Architecture

| **Component**        | **Dev/Edge Profile**              | **Enterprise Profile**
| :--- | :--- | :--- |
| Control Plane        | Single Node OpenShift             | Multi-node OpenShift
| Virtualization       | OpenShift Virtualization Operator | OpenShift Virtualization Operator
| Storage              | LVM Storage Operator (LVMS)       | OpenShift Data Foundation (ODF)
| Network              | Projects, Routes                  | Projects, Routes, netpol, LoadBalancer and VPN
| VM                   | Container Image and VM Template   | Container Image and VM Template
| Identity and Access  | Keycloak                          | Keycloak
  
## Repository Structure  
  
```plaintext
.
├── LICENSE
├── manifests
│   └── base
│       ├── 01-openshift-virt.yaml      # Virtualization | Install Virtualization Operator
│       ├── 02-hyperconverged.yaml      # Virtualization | Deploy Virtualization Control Plane Workload
│       ├── 03-lvms.yaml                # Storage        | Install Storage Operator
│       ├── 04-lvmcluster.yaml          # Storage        | Deploy Block Storage
│       ├── 05-vm-datavolume.yaml       # VM             | DataVolume 
│       ├── 06-vdi-space.yaml
│       └── 07-fedora-vdi-template.yaml
└── README.md
```
  
## Quickstart (Development Profile)  
1. Install SNO via assisted installer.
2. Login to the cluster via CLI using the kubeadmin credentials and kubeconfig file.
   `$ export KUBECONFIG=~/path/to/kubeconfig`  
   `$ oc login https://API:6443 -u kubeadmin`  
3. Apply manifests.

## Roadmap and Future Work
* [x] Control Plane: Deploy Single Node OpenShift
* [x] Virtualization: Deploy OpenShift Virtualization
* [x] Storage: Deploy LVM Storage Operator
* [] Networking: Deploy svc and routes
* [] VM: Create Container Image and Deploy VM Template
* [] Identity and Access Management: Deploy Keycloak
