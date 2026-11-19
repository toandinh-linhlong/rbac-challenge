# Technical Assessment: Role-Based Access Control (RBAC) System

## Overview

Welcome to the backend engineering technical assessment. Your task is to design and implement a **Role-Based Access Control (RBAC)** system with data-level scoping for two core modules: **Inventory** and **Warehouse**.

We are evaluating not only your working implementation, but also your **architectural reasoning**. You will be expected to explain why you chose your specific design—covering data modeling, authorization enforcement, trade-offs, and scalability.

---

## Functional Requirements

### 1. Users, Branches, and Roles
* **Super Admin:** Has full system access and can create, update, and manage **Roles** and their associated permissions.
* **Role Assignment:** Every user belongs to a **Branch** and is assigned a **Role** *(if you choose to support multiple roles per user, document how you resolve permission conflicts)*.
* **Dynamic Permissions:** Permissions must be configurable per role at runtime rather than hardcoded to static role names (e.g., avoid `if (user.role === 'MANAGER')`).
* **Deny by Default:** If a permission is not explicitly granted to a user's role, access must be denied.

### 2. Module Permissions & Data Scoping

#### Inventory APIs
| Action | Allowed Scopes |
| :--- | :--- |
| **View** | `None` \| `Own` *(records created by/assigned to the logged-in user)* \| `All` |
| **Create** | `Denied` \| `Allowed` |
| **Update** | `None` \| `Own` \| `All` |
| **Delete** | `None` \| `Own` \| `All` |

#### Warehouse APIs
| Action | Allowed Scopes |
| :--- | :--- |
| **View** | `None` \| `Own Branch` *(warehouses belonging to the user's branch)* \| `All` |
| **Create** | `Denied` \| `Allowed` |
| **Update** | `None` \| `Own Branch` \| `All` |
| **Delete** | `None` \| `Own Branch` \| `All` |
| **Store Inventory to Warehouse** | `Denied` \| `Allowed` *(subject to the contextual rule below)* |

### 3. Contextual Authorization Rule
* **`StoreInventoryToWarehouse`:** Even when a user's role has permission to perform this action, the system must verify that the **target warehouse belongs to the current user's branch**. If it does not, the request must be rejected with an appropriate HTTP status code (e.g., `403 Forbidden`).

---

## Deliverables

### 1. Working Implementation
* **API Endpoints:** REST or GraphQL endpoints covering Role Management, Inventory CRUD, Warehouse CRUD, and `StoreInventoryToWarehouse`.
* **Authentication:** A lightweight auth mechanism (e.g., JWT or a mock auth header/middleware) is completely fine—please focus your effort on **authorization**.
* **Verification:** Include database seed data and/or automated tests demonstrating different roles, users, and branch boundaries (including negative test cases where access should be denied).
* **Setup Instructions:** Clear instructions on how to install dependencies, run migrations/seeds, and start the application locally.

### 2. Design & Architecture Explanation
Please include a `DESIGN.md` (or add a section to your project's `README.md`) addressing the following points. Be prepared to walk through these during the technical review:

1. **Data Modeling:** How did you model users, branches, roles, permissions, and scopes in your database? Why this schema?
2. **Enforcement Layer:** Where do your action-level and data-scoping checks live (e.g., middleware, policy guards, service layer, query filters) and why?
3. **Extensibility:** How would your architecture accommodate a new module (e.g., *Orders*) or a new scope (e.g., *Region-level*) without major refactoring?
4. **Production Trade-offs:** What shortcuts did you take for this assessment, and what would you change for a production environment (e.g., permission caching, invalidating stale tokens when roles change)?

---

## Evaluation Criteria

* **Correctness & Security:** Accurate enforcement of both action-level and resource-level permissions, including protection against IDOR (Insecure Direct Object Reference).
* **Code Structure:** Clean separation of concerns between business logic and authorization logic.
* **Scalability & Maintainability:** Extensible design without brittle conditional chains tied to hardcoded roles.
* **Technical Communication:** Clear justification of your architectural decisions and awareness of alternative approaches.

---

## Submission Instructions

1. Push your solution to a Git repository (or provide a `.zip` archive if instructed).
2. Ensure all setup commands and test instructions are documented and verified on a clean environment.
3. Include a brief note on the tech stack used and any assumptions you made.
