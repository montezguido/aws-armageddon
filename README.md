# Multi-Region AWS Telemedicine Infrastructure Design

Terraform architecture prototype for a globally distributed telemedicine platform with regional application presence and data-residency constraints centered on Japan.

## Project Overview

This project models an AWS network architecture for a healthcare organization expanding a telemedicine service beyond Japan. The design accounts for users traveling internationally while keeping sensitive data handling and centralized logging requirements aligned with Japan-based controls.

The architecture spans seven AWS regions and uses Terraform to define regional provider mappings, VPC address space, and public/private subnet plans.

> **Portfolio note:** This repository originated from a group lab scenario and is presented here as an infrastructure design case study. The current Terraform is an architecture prototype, not a production-ready deployment.

## Business and Technical Requirements

The scenario required the architecture to support:

- A primary application environment in Tokyo
- Regional application presence near international users
- Multiple Availability Zones per regional environment
- Public and private network segmentation
- A path for centralized logging back to Japan
- Restrictions on storing sensitive information outside Japan
- Infrastructure that could be expressed and maintained as code

## Regional Architecture

| Location | AWS Region | VPC CIDR |
|---|---|---|
| Tokyo | `ap-northeast-1` | `10.40.0.0/16` |
| New York | `us-east-1` | `10.41.0.0/16` |
| London | `eu-west-2` | `10.42.0.0/16` |
| São Paulo | `sa-east-1` | `10.43.0.0/16` |
| Australia | `ap-southeast-2` | `10.44.0.0/16` |
| Hong Kong | `ap-east-1` | `10.45.0.0/16` |
| California | `us-west-1` | `10.46.0.0/16` |

A separate Tokyo test VPC is also represented in the variable model.

## Terraform Structure

| File | Purpose |
|---|---|
| `00-variables.tf` | Defines regional mappings, VPC CIDR blocks, and subnet addressing |
| `01-provider.tf` | Declares AWS provider aliases for each target region |
| `02-vpc.tf` | Defines the VPC resource model |
| `03-subnets.tf` | Contains the subnet implementation draft |
| `4-transit-gateway.tf` | Reserved for future inter-region connectivity work |
| `Terraform.gitignore` | Terraform-specific ignore rules |

## Engineering Decisions

### Structured IP Addressing

Each region receives a dedicated `/16` VPC address range, with `/24` networks reserved for public and private subnets. This makes the regional topology easier to understand and reduces the chance of overlapping address space when connectivity is added later.

### Regional Provider Aliases

The Terraform provider configuration includes aliases for each target AWS region. This establishes the foundation for creating resources in multiple geographic locations from a common codebase.

### Public and Private Segmentation

The address model separates public-facing application components from private workloads. The private network design is particularly important for logging and data-handling components that should not be directly internet accessible.

### Infrastructure as Code

VPCs and subnet definitions are modeled through Terraform variables rather than manually duplicated configuration. The intent is to make regional infrastructure repeatable, reviewable, and easier to maintain.

## Current Implementation Status

The repository currently demonstrates the **architecture and Terraform data model**, including:

- Multi-region AWS provider aliases
- Regional VPC definitions
- Structured CIDR planning
- Public/private subnet mappings
- A draft approach for subnet creation
- Planning for future transit connectivity

The current code should be treated as a prototype. The subnet resource is still commented out, and the VPC implementation requires further refactoring so resources are explicitly associated with the correct aliased providers before this can be considered a deployable multi-region configuration.

## Planned Technical Improvements

The next iteration should:

1. Refactor regional infrastructure into reusable Terraform modules.
2. Pass the appropriate AWS provider alias into each regional module.
3. Enable and validate public/private subnet resources.
4. Add route tables, internet gateways, NAT strategy, and security groups.
5. Add compute or Auto Scaling resources across multiple Availability Zones.
6. Implement secure inter-region connectivity for centralized services.
7. Add logging and monitoring controls with Japan-based data residency.
8. Run `terraform fmt`, `terraform validate`, and a controlled `terraform plan` before deployment.

## Skills Demonstrated

- AWS architecture planning
- Terraform / Infrastructure as Code
- Multi-region network design
- VPC and subnet design
- CIDR planning
- Public/private network segmentation
- Data-residency-aware architecture
- Cloud security design
- Technical documentation

## Key Takeaway

The value of this project is the architecture problem itself: designing a distributed cloud network that balances global availability with security, network segmentation, and geographic data-handling constraints. The Terraform code captures the initial infrastructure model and provides a foundation for a production-grade modular implementation.
