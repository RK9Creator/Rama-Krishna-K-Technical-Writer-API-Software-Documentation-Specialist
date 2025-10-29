# GRS Tool - User Guide

---

## Contents

1. [Introduction](#1-introduction)  
2. [System Overview](#2-System-Overview)
   -2.1. [GRS Architecture](#21-GRS-Architecture)
4. [GRS Tool overview](#3-GRS-tool-overview)  
5. [Working with the GRS Tool](#4-working-with-the-GRS-tool)  
   - 4.1. [Upload design logs](#41-upload-design-logs)  
   - 4.2. [Validate records](#42-validate-records)  
   - 4.3. [Edit records](#43-edit-records)  
   - 4.4. [Delete records](#44-delete-records)  
   - 4.5. [Migrate records](#45-migrate-records)  
6. [Troubleshooting](#5-troubleshooting)  
7. [Best practices](#6-best-practices)  
8. [Glossary](#7-glossary)  
9. [Appendices](#8-appendices)  
10. [Document control](#9-document-control)  

---

## 1. Introduction

## About This Document

### Purpose
This document provides step-by-step instructions for using the **Global Repository System (GRS)**. It helps Configuration, QA, and IT professionals manage design logs and perform configuration validation activities within the **Facets ecosystem**.

### Scope
This guide covers GRS features, workflows, and best practices for uploading, validating, editing, deleting, and migrating records across multiple environments.

### Intended Audience
- Configuration Team Members  
- QA Analysts  
- System Administrators  
- IT Support Staff  

### Prerequisites
Users should be familiar with:
- Facets configuration principles  
- SQL database basics  
- Internal migration workflow (1D → 1R → 1P)

---

## Summary
The **Global Repository System (GRS)** is a healthcare data management tool that automates configuration activities within the Facets environment.  
It allows configuration teams to upload design logs, validate data, correct failed records, and migrate approved records securely.

**Key Benefits:**
- Automated data validation and error detection  
- Reduced manual SQL or Facets operations  
- Full audit trails for every record update  
- Secure data migration between environments  

---

## 2. System Overview

### 2.1 GRS Architecture
| Component | Description |
|------------|--------------|
| **GRS Front-End** | User interface for uploading, validating, and migrating records. |
| **SQL Database** | Back-end storage for validated records and audit logs. |
| **Facets Application** | Front-end system reflecting migrated configuration data. |

### 2.2 Workflow Logic
1. Upload design log (.csv or .xlsx).  
2. System performs multi-stage validation:  
   - Format validation  
   - Business rule validation  
   - Relational validation  
3. Validated data is stored in SQL.  
4. Users correct failed records.  
5. Admins migrate validated data to Facets via secure APIs.

### 2.3 Supported Environments
- **1D (Development):** Validation and testing.  
- **1R (QA):** Quality review and approval.  
- **1P (Production):** Final deployment.  

---

## 3. User Roles and Permissions
| Role | Responsibilities | Access Level |
|------|------------------|--------------|
| **Configuration User** | Uploads and validates design logs; corrects failed records. | Upload, Validate, Edit |
| **QA User** | Reviews and approves records for migration. | Validate, Comment |
| **Admin** | Approves migrations and manages user permissions. | Full Access |

---

## 4. Interface Overview
The **GRS** interface includes the following sections:

- **Dashboard:** Displays overall validation, migration, and error statistics.  
- **Upload Design Log:** Interface to upload `.csv` or `.xlsx` files.  
- **Validation Table:** Displays validation results with color-coded statuses.  
- **Migration Panel:** Initiates data migration to Facets.  
- **Activity Log:** Tracks all user actions (upload, edit, delete, migrate).  

---

## 5. Working with GRS

### 5.1 Uploading Design Logs
1. Log in to GRS.  
2. Go to **Upload Design Log**.  
3. Select the `.csv` or `.xlsx` file.  
4. Click **Upload**.  
5. Review the validation summary.

> **Tip:** Include version and date in the filename (e.g., `DesignLog_2025-10-28_v2.xlsx`).

---

### 5.2 Validating Records
- GRS automatically validates each record on upload.  
- Failed records are displayed in red with detailed error messages (e.g., `VAL-PLAN-001: Invalid Plan Code`).  
- Correct records directly in GRS or re-upload the fixed file.  
- Re-run validation until all records pass.  

---

### 5.3 Editing and Deleting Records
- **Edit:** Click the **Edit** icon, make corrections, and click **Save**.  
- **Delete:** Click **Delete** and confirm.  
- Every action is logged with user ID, timestamp, and record ID.  

---

### 5.4 Migrating Validated Records
- Only validated records can be migrated.  
- Migration path: **1D → 1R → 1P**.  
- Admin approval is required before migration.  
- Migration triggers secure REST API calls to load data into Facets.  

---

## 6. Integration with SQL and Facets

### 6.1 Front-End and Back-End Relationship
| Function | System | Description |
|-----------|---------|-------------|
| **Single Record Editing** | Facets Application | Used for individual record updates after migration. |
| **Bulk Record Editing** | SQL Database | Enables bulk updates at the database level. |
| **Validation and Migration** | GRS | Centralized platform for validation and migration. |

### 6.2 Process Flow
1. Upload the design log with configuration data.  
2. GRS performs validation checks and flags errors.  
3. Validated data is stored in SQL.  
4. GRS triggers REST APIs for migration to Facets.  
5. Records remain synchronized between SQL and Facets.  
6. All user actions are logged for traceability.  

---

## 7. Troubleshooting
| Issue | Cause | Resolution |
|--------|--------|-------------|
| **Upload Error 400** | Header mismatch | Ensure the file matches the template format. |
| **Validation Timeout** | File too large | Split the file and re-upload smaller parts. |
| **Migration Blocked** | Insufficient permissions | Contact the Admin for access. |
| **SQL Sync Delay** | API/network lag | Wait or retry after a few minutes. |

---

## 8. Best Practices
- Validate records in **1D** before migrating to QA.  
- Keep backup copies of all uploaded logs.  
- Avoid manual edits in Facets after migration.  
- Review the **Activity Log** weekly.  
- Always use the latest file templates.  

---

## 9. FAQs
**Q1. Why was the GRS developed?**  
To automate configuration uploads and reduce manual Facets and SQL tasks.  

**Q2. Can I still update data in SQL or Facets?**  
Yes, but GRS ensures validations and audit compliance.  

**Q3. How does GRS ensure accuracy?**  
Through layered validation and synchronization between SQL and Facets.  

**Q4. How are deleted records tracked?**  
Deleted records are logged in SQL with user ID and timestamp.  

---

## 10. Glossary
| Term | Definition |
|------|-------------|
| **1D / 1R / 1P** | Development, QA, and Production environments. |
| **Design Log** | Configuration file (`.csv` / `.xlsx`) uploaded to GRS. |
| **Facets** | Healthcare administration platform for configuration. |
| **SQL Database** | Back-end system for data storage and validation. |
| **API** | Application Programming Interface for system communication. |
| **Validation** | Automatic data checking process in GRS. |

---

## 11. Document Control
| Version | Date | Author | Description |
|----------|------|---------|-------------|
| 1.0 | 15-Sep-2025 | Rama Krishna K | Initial Release |
| 2.0 | 30-Sep-2025 | Rama Krishna K | Structural Enhancements |
| 3.0 | 15-Oct-2025 | Rama Krishna K | SQL–Facets Integration |
| 4.0 | 29-Oct-2025 | Rama Krishna K | Microsoft-Style Rewrite |
