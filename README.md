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

## Prerequisites Matrix

| **Component**        | **Dev/Edge Profile**              | **Enterprise Profile**
| :--- | :--- | :--- |
| Control Plane        | Single Node OpenShift             | Multi-node OpenShift
| Virtualization       | OpenShift Virtualization Operator | OpenShift Virtualization Operator
| Storage Provisioner  | LVM Storage Operator (LVMS)       | OpenShift Data Foundation (ODF)
| Storage Capabilities | RWO, Thin Prov. via LVMS          | RWX, Instant PVC Cloning, Live Migration
| Ingress/Routing      | Standard OCP Route                | LoadBalancer

## Architectural Concepts and Design Decisions  

### 1. Ephemeral First, Persistent when Necessary  
* **Stateless Desktop Pools:** Desktops are deployed as stateless, disposable VMs.  
* **Persistent User Profiles:** User data and configurations are decoupled from the OS disk and mounted dynamically.  
  
Reason: Efficiency and stability in user experience.  
  
### 2. GitOps Native Management  
* All desktop definitions, image sources, and network policies are defined declaratively.  
* Fleet management, updates, and scaling are handled through GitOps.  
  
Reason: Repeatability, scalability.  
  
### 3. Containerised Base Images  
* Base OS images are packaged as OCI-compliant `ContainerDisk` images stored in an image registry.  
* Updates to the desktop OS follow standard CI/CD container build pipelines.  
  
Reason: Auditability, consistency, scalability.  
  
### 4. Zero-Client Remote Protocol Proxying  
* Sessions are delivered via web browsers using HTML5 client streaming proxies.  
* Users access desktops over secure HTTPS via standard OpenShift Routes.  
  
Reason: More stable user experience by reducing dependencies.  
  
## Repository Structure  
  
```plaintext
.
├── manifests/
│   ├── base/                  # Core KubeVirt templates and preferences
│   │   ├── datavolumes/       # Base OS disk definitions
│   │   ├── vm-templates/      # VirtualMachine custom resources
│   │   └── routes/            # OpenShift Routes & Ingress config
│   └── overlays/              # Environment customizations
│       ├── dev-sno/           # Configuration tuned for Single Node OpenShift (LVMS)
│       └── enterprise-odf/    # Configuration tuned for ODF (RWX / Fast Cloning)
├── automation/
│   ├── cloud-init/            # Ignition & cloud-init user data scripts
│   └── containerdisks/        # Containerfiles for OS base images
└── README.md
```
  
## Quickstart (Development Profile)  

## Roadmap and Future Work
* [x] Control Plane: Deploy Single Node OpenShift
* [] Virtualization: Deploy OpenShift Virtualization
* [] Storage: Deploy LVM Storage Operator
* [] Networking: Deploy svc and routes
* [] Identity and Access Management: Deploy Keycloak
* [] Desktop container images: Create a minimal viable desktop image (I just need a browser)
