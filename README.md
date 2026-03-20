# 🛒 ECA E-Commerce Cloud Platform

> **ITS 2130 — Enterprise Cloud Architecture | Final Project**
> Higher Diploma in Software Engineering @ IJSE

![Java](https://img.shields.io/badge/Java-25-orange?style=flat-square&logo=openjdk)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-Latest-brightgreen?style=flat-square&logo=springboot)
![Spring Cloud](https://img.shields.io/badge/Spring%20Cloud-Enabled-brightgreen?style=flat-square&logo=spring)
![GCP](https://img.shields.io/badge/Deployed%20on-GCP-blue?style=flat-square&logo=googlecloud)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)

---

## 🌐 Live Eureka Dashboard

> **🔗 [[http://YOUR_EUREKA_PUBLIC_IP:8761](http://YOUR_EUREKA_PUBLIC_IP:8761)](https://service-registry-1099446496316.asia-south1.run.app)**
> *(Replace with your actual public Eureka URL)*

---

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Repository Structure](#repository-structure)
- [Technology Stack](#technology-stack)
- [Microservices](#microservices)
- [Platform Components](#platform-components)
- [Cloud Infrastructure (GCP)](#cloud-infrastructure-gcp)
- [Database Design](#database-design)
- [Cloud Storage](#cloud-storage)
- [High Availability & Auto Scaling](#high-availability--auto-scaling)
- [Process Management (PM2)](#process-management-pm2)
- [Screenshots](#screenshots)
- [Getting Started](#getting-started)
- [Submodules](#submodules)

---

## Overview

This project is a cloud-native, microservice-based e-commerce platform designed and deployed on **Google Cloud Platform (GCP)**. It demonstrates enterprise cloud architecture patterns including service discovery, centralized configuration, API gateway routing, horizontal auto-scaling, and process-level fault tolerance.

The system consists of **3 business microservices**, **3 platform services**, and **1 frontend application**, all deployed across GCP VM Instance Groups with load balancing, Cloud SQL, Firestore, and Cloud Storage integration.

---

## Architecture

```
                        ┌─────────────────────────────────────────────────┐
                        │              Google Cloud Platform               │
                        │                                                 │
  Client ──────────────▶│  Load Balancer                                  │
  (Browser)             │       │                                         │
                        │       ▼                                         │
                        │  ┌─────────────┐     ┌──────────────────────┐  │
                        │  │ API Gateway │────▶│  Config Server        │  │
                        │  │  (Port 8080)│     │  (Centralized Config) │  │
                        │  └──────┬──────┘     └──────────────────────┘  │
                        │         │                                        │
                        │         │  Service Discovery via Eureka          │
                        │         ▼                                        │
                        │  ┌──────────────────────────────────────────┐   │
                        │  │            Eureka Service Registry        │   │
                        │  └──────┬──────────────┬───────────────┬────┘   │
                        │         │              │               │         │
                        │         ▼              ▼               ▼         │
                        │  ┌────────────┐ ┌───────────┐ ┌──────────────┐ │
                        │  │  Product   │ │  Order    │ │ Notification │ │
                        │  │  Service   │ │  Service  │ │   Service    │ │
                        │  │ (MySQL)    │ │ (MongoDB) │ │  (MongoDB)   │ │
                        │  └────────────┘ └───────────┘ └──────────────┘ │
                        │         │                                        │
                        │         ▼                                        │
                        │  ┌────────────────────────────────────────────┐ │
                        │  │     GCS Bucket │ Cloud SQL │ Firestore     │ │
                        │  └────────────────────────────────────────────┘ │
                        └─────────────────────────────────────────────────┘
```

---

## Repository Structure

This project follows a **Polyrepo with Git Submodules** architecture as required by the project guidelines.

```
ecommerce-cloud-project/          ← Main monorepo (this repo)
│
├── Platform/
│   ├── config-server/            → Git Submodule
│   ├── service-registry/         → Git Submodule
│   └── api-gateway/              → Git Submodule
│
├── Service/
│   ├── product-service/          → Git Submodule
│   ├── order-service/            → Git Submodule
│   └── notification-service/     → Git Submodule
│
├── frontend/                     → Git Submodule
│
└── .gitmodules
```

---

## Technology Stack

| Category | Technology |
|---|---|
| Language | Java 25 |
| Framework | Spring Boot (Latest) |
| Cloud | Spring Cloud |
| Data | Spring Data JPA / Spring Data MongoDB |
| Service Discovery | Netflix Eureka |
| API Gateway | Spring Cloud Gateway |
| Config | Spring Cloud Config Server |
| Relational DB | MySQL (Cloud SQL) |
| Non-Relational DB | MongoDB (Firestore / Atlas) |
| File Storage | Google Cloud Storage (GCS) |
| Process Manager | PM2 |
| Cloud Provider | Google Cloud Platform (GCP) |
| Frontend | HTML / CSS / JavaScript |

---

## Microservices

### 🛍️ Product Service
- **Repository:** [eca-product-service](https://github.com/GihanViduranga/eca-product-service)
- **Database:** MySQL (Cloud SQL)
- **Responsibilities:** Product catalog management, image upload to GCS bucket
- **Port:** 8081

### 📦 Order Service
- **Repository:** [eca-order-service](https://github.com/GihanViduranga/eca-order-service)
- **Database:** MongoDB
- **Responsibilities:** Order placement, order status tracking
- **Port:** 8082

### 🔔 Notification Service
- **Repository:** [eca-notification-service](https://github.com/GihanViduranga/eca-notification-service)
- **Database:** MongoDB
- **Responsibilities:** Email/event-driven notifications triggered by order events
- **Port:** 8083

---

## Platform Components

### ⚙️ Config Server
- **Repository:** [eca-config-server](https://github.com/GihanViduranga/eca-config-server)
- Centralizes and externalizes configuration for all microservices
- Sources configuration from the `configurations` repository

### 🗂️ Service Registry (Eureka)
- **Repository:** [eca-service-registry](https://github.com/GihanViduranga/eca-service-registry)
- All microservices register here on startup
- Enables client-side load balancing and service discovery
- **Dashboard:** 🔗 [http://YOUR_EUREKA_PUBLIC_IP:8761](http://YOUR_EUREKA_PUBLIC_IP:8761)

### 🚪 API Gateway
- **Repository:** [eca-api-gateway](https://github.com/GihanViduranga/eca-api-gateway)
- Single entry point for all backend APIs
- Routes requests to appropriate microservices via Eureka

---

## Cloud Infrastructure (GCP)

The following GCP resources are provisioned and visible in the GCP Console:

| Resource | Purpose |
|---|---|
| **VM Instance Groups** | Auto-scaling groups for each microservice |
| **VM Instance Templates** | Base templates for consistent VM provisioning |
| **Virtual Machines** | Hosts for all services managed via PM2 |
| **Disk Images** | Pre-configured images for fast instance startup |
| **Health Checks** | Monitor service health for auto-healing |
| **Load Balancer** | Distributes traffic across VM instances |
| **Cloud DNS** | Domain name resolution for services |
| **Cloud NAT Gateway** | Outbound internet for private VMs |
| **Cloud Router** | Routes traffic within VPC |
| **VPC Network** | Isolated private network for all resources |
| **Firewall Rules** | Controls inbound/outbound traffic per service |
| **Cloud SQL** | Managed MySQL for Product Service |
| **Firestore** | NoSQL database for notification/order data |
| **Cloud Storage Buckets** | Product image storage |

---

## Database Design

This project mandatorily uses **both relational and non-relational databases**:

| Service | Database Type | Technology |
|---|---|---|
| Product Service | ✅ Relational | MySQL via Cloud SQL |
| Order Service | ✅ Non-Relational | MongoDB |
| Notification Service | ✅ Non-Relational | MongoDB |

---

## Cloud Storage

The **Product Service** integrates with a **Google Cloud Storage bucket** to store product images.

- Products can be created with image uploads
- Images are stored in a GCS bucket and served via public/signed URL
- This functionality is demonstrated through the frontend application

---

## High Availability & Auto Scaling

### Microservice Auto Scaling
- All 3 business microservices are deployed using **VM Instance Groups**
- Instance groups are configured with **auto-scaling policies** based on CPU utilization
- Minimum 2 instances per service to eliminate single points of failure

### Platform High Availability
- Config Server, Service Registry, and API Gateway run as **multiple instances**
- Instances are distributed across **multiple zones** within the GCP region
- Ensures fault tolerance: if one zone fails, other zones continue serving traffic

---

## Process Management (PM2)

All application processes on the VMs are managed using **PM2**:

```bash
# Check running services
pm2 monit

# List all processes
pm2 list

# View logs
pm2 logs

# PM2 starts automatically on VM restart
pm2 startup
pm2 save
```

PM2 ensures:
- ✅ Applications start automatically on VM boot
- ✅ Logs are written to a centralized location
- ✅ Services automatically restart on failure (process-level fault tolerance)
- ✅ Zero manual intervention needed for crash recovery

---

## Screenshots

### GCP Console — Cloud Infrastructure
> *Add your GCP Console screenshot here*

![GCP CONSOLE](https://res.cloudinary.com/du756xpbg/image/upload/v1773988245/Screenshot_2026-03-20_113810_wvlqip.png)

### Eureka Dashboard — Registered Services

![Eureka Dashboard](https://res.cloudinary.com/du756xpbg/image/upload/v1773988244/Screenshot_2026-03-20_113253_uees5y.png)


### Frontend Application
![Frontend1](https://res.cloudinary.com/du756xpbg/image/upload/v1773988244/Screenshot_2026-03-20_113358_uultxs.png)

![Frontend2](https://res.cloudinary.com/du756xpbg/image/upload/v1773988244/Screenshot_2026-03-20_113507_pkeyrt.png)

Check out a video walkthrough of Project:  
[![Watch the video](https://res.cloudinary.com/du756xpbg/image/upload/v1773988244/Screenshot_2026-03-20_113358_uultxs.png)](https://res.cloudinary.com/du756xpbg/video/upload/v1773989876/Untitled_design_b1aopr.mp4)

## Getting Started

### Clone with all submodules

```bash
git clone --recurse-submodules https://github.com/GihanViduranga/ecommerce-cloud-project.git
```

### Update submodules (if already cloned)

```bash
git submodule update --init --recursive
```

### Run locally (development)

```bash
# 1. Start Config Server first
cd Platform/config-server
./mvnw spring-boot:run

# 2. Start Service Registry
cd Platform/service-registry
./mvnw spring-boot:run

# 3. Start Microservices
cd Service/product-service && ./mvnw spring-boot:run
cd Service/order-service && ./mvnw spring-boot:run
cd Service/notification-service && ./mvnw spring-boot:run

# 4. Start API Gateway
cd Platform/api-gateway
./mvnw spring-boot:run
```

---

## Submodules

| Module | Repository | Description |
|---|---|---|
| `Platform/config-server` | [eca-config-server](https://github.com/GihanViduranga/eca-config-server) | Spring Cloud Config Server |
| `Platform/service-registry` | [eca-service-registry](https://github.com/GihanViduranga/eca-service-registry) | Netflix Eureka Registry |
| `Platform/api-gateway` | [eca-api-gateway](https://github.com/GihanViduranga/eca-api-gateway) | Spring Cloud API Gateway |
| `Service/product-service` | [eca-product-service](https://github.com/GihanViduranga/eca-product-service) | Product catalog (MySQL + GCS) |
| `Service/order-service` | [eca-order-service](https://github.com/GihanViduranga/eca-order-service) | Order management (MongoDB) |
| `Service/notification-service` | [eca-notification-service](https://github.com/GihanViduranga/eca-notification-service) | Notifications (MongoDB) |
| `frontend` | [eca-frontend](https://github.com/GihanViduranga/eca-frontend) | Web frontend application |

---

## 👨‍💻 Author

**Gihan Viduranga**
Higher Diploma in Software Engineering
IJSE — ITS 2130 Enterprise Cloud Architecture

---

*Submitted for ITS 2130 Final Project — March 2026*
