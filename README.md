# 🚀 Enterprise Multi-Tenant SaaS Platform on Amazon EKS

<p align="center">
  <img src="https://img.shields.io/badge/AWS-EKS%20Auto%20Mode-orange?style=for-the-badge&logo=amazonaws" alt="AWS EKS">
  <img src="https://img.shields.io/badge/Orchestration-kro-blue?style=for-the-badge&logo=kubernetes" alt="kro">
  <img src="https://img.shields.io/badge/GitOps-ArgoCD-red?style=for-the-badge&logo=argo" alt="ArgoCD">
  <img src="https://img.shields.io/badge/Infrastructure-Terraform-purple?style=for-the-badge&logo=terraform" alt="Terraform">
</p>

A production-grade, enterprise-ready reference implementation for building, securing, and scaling multi-tenant SaaS platforms on **Amazon Elastic Kubernetes Service (Amazon EKS)**. This architecture leverages **EKS Auto Mode**, **kro (Kubernetes Resource Orchestrator)**, **ACK (AWS Controllers for Kubernetes)**, and **Argo CD GitOps** workflows.

---

## 📊 Enterprise Architecture Overview

The following SVG diagram illustrates the hub-spoke control plane and data plane topology, separating core cluster management from tenant-isolated workloads:

\\\svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 800 350" width="100%" height="100%">
  <style>
    .bg { fill: #0d1117; }
    .box { fill: #161b22; stroke: #30363d; stroke-width: 2px; rx: 8px; }
    .title { fill: #58a6ff; font-family: -apple-system, sans-serif; font-weight: bold; font-size: 14px; }
    .text { fill: #c9d1d9; font-family: -apple-system, sans-serif; font-size: 12px; }
    .arrow { stroke: #8b949e; stroke-width: 2px; fill: none; marker-end: url(#arrow); }
  </style>
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="5" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M 0 0 L 10 5 L 0 10 z" fill="#8b949e"/>
    </marker>
  </defs>
  
  <rect width="800" height="350" class="bg"/>

  <!-- Control Plane Hub -->
  <rect x="50" y="50" width="220" height="250" class="box"/>
  <text x="70" y="80" class="title">Control Plane Hub</text>
  <text x="70" y="120" class="text">• Argo CD GitOps Engine</text>
  <text x="70" y="150" class="text">• kro Controller Manager</text>
  <text x="70" y="180" class="text">• ACK Controllers</text>
  <text x="70" y="210" class="text">• Multi-Tenant Blueprints</text>

  <!-- Data Plane Spoke -->
  <rect x="530" y="50" width="220" height="250" class="box"/>
  <text x="550" y="80" class="title">Data Plane Spoke</text>
  <text x="550" y="120" class="text">• Tenant-001 (Basic / Shared)</text>
  <text x="550" y="150" class="text">• Tenant-002 (Basic / Shared)</text>
  <text x="550" y="180" class="text">• Tenant-004 (Pro / Dedicated)</text>
  <text x="550" y="210" class="text">• Karpenter NodePools</text>

  <!-- Connecting Arrow -->
  <path d="M 270 175 L 510 175" class="arrow"/>
  <text x="330" y="160" fill="#8b949e" font-family="sans-serif" font-size="10px">GitOps Sync & RGDs</text>
</svg>
\\\

---

## 📋 Prerequisites

Before deploying or validating this repository, ensure your local workstation or build environment has the following tools installed and configured:

* **AWS CLI** (v2.x configured with appropriate IAM credentials and administrative permissions)
* **kubectl** (v1.30+) for interacting with Amazon EKS control and data plane clusters
* **Git** for repository management and version control
* **Terraform** (v1.5+) for infrastructure provisioning
* **Helm** (v3.x) for chart dependency management

---

## 🏗️ Lab Architecture & Visual Reference (lab4.docx)

| Module / Topic | Technical Component | Lab Screenshot Reference & Key Highlights |
| :--- | :--- | :--- |
| **1. Hub-Spoke Foundation** | Control Plane Hub & Data Plane Spoke | **Argo CD Dashboard (Image 61)**: Highlights Healthy state, Synced status against main, and active tenant topology trees (	enant-001, 	enant-002, 	enant-003) alongside cluster network policies. |
| **2. Blueprint Orchestration** | kro ResourceGraphDefinitions (RGDs) | **VS Code Explorer (Image 69)**: Demonstrates the modular structure of saas-workloads/examples/kro/ and terminal commands copying manifests into active deployment paths. |
| **3. Cost Attribution** | EKS Split Cost Allocation & QuickSight | **QuickSight Dashboard (Image 10)**: Tracks total split cost (.21), cluster-level compute distribution, and per-namespace expense mapping (	enant-004, 	enant-002, 	enant-001). |

---

## 🚀 Quick Start & Verification

### 1. Verify Cluster Contexts
Ensure your local kubectl is configured to target the data plane cluster:
\\\ash
kubectl config use-context data-plane
kubectl config get-contexts
\\\

### 2. Test Runtime Guardrails (PSA Baseline)
Verify that Pod Security Admission strictly rejects privileged container configurations at admission time:
\\\ash
kubectl apply -f saas-workloads/examples/security/privileged-pod.yaml -n tenant-001
\\\
*Expected Output:* pods "privileged-demo" is forbidden: violates PodSecurity "baseline:latest": privileged...

---

## 🧹 Cleanup Instructions

To completely tear down the environment, delete all provisioned cloud infrastructure, and avoid ongoing AWS charges, execute the following clean-up sequence:

1. **Delete Tenant Workloads via GitOps / kubectl:**
   \\\ash
   kubectl delete -f saas-workloads/tenants/ --ignore-not-found=true
   \\\

2. **Destroy Infrastructure via Terraform:**
   Navigate to your terraform workspace directory and destroy the provisioned stacks:
   \\\ash
   cd terraform/
   terraform destroy -auto-approve
   \\\

3. **Purge Argo CD Applications:**
   Remove any remaining custom resource definitions and application sync controllers from the control plane hub cluster.

---

## 🔗 References & External Web Links

* [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/)
* [Kubernetes Resource Orchestrator (kro)](https://kro.run/)
* [Argo CD - Declarative GitOps CD for Kubernetes](https://argo-cd.readthedocs.io/)
* [AWS Controllers for Kubernetes (ACK)](https://aws-controllers-k8s.github.io/community/)
* [Karpenter Kubernetes Autoscaler](https://karpenter.sh/)
