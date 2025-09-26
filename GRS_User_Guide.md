# GRS Tool - User Guide

---

## Contents

1. [Introduction](#1-introduction)  
2. [Getting started](#2-getting-started)  
3. [GRS Tool overview](#3-GRS-tool-overview)  
4. [Working with the GRS Tool](#4-working-with-the-GRS-tool)  
   - 4.1. [Upload design logs](#41-upload-design-logs)  
   - 4.2. [Validate records](#42-validate-records)  
   - 4.3. [Edit records](#43-edit-records)  
   - 4.4. [Delete records](#44-delete-records)  
   - 4.5. [Migrate records](#45-migrate-records)  
5. [Troubleshooting](#5-troubleshooting)  
6. [Best practices](#6-best-practices)  
7. [Glossary](#7-glossary)  
8. [Appendices](#8-appendices)  
9. [Document control](#9-document-control)  

---

## 1. Introduction

### Purpose
Use this guide to learn how to upload, validate, update, and migrate design log records with the GRS Tool.

### Audience
This guide is for:  
- Configuration teams  
- QA teams  
- IT support teams  

### Scope
This guide explains:  
- Uploading design logs  
- Validating records  
- Editing or deleting records  
- Migrating records across environments  

### Prerequisites
Before you begin, make sure you have:  
- Access to the 1D development region  
- A valid user account with role-based permissions  
- Knowledge of design log structure  

---

## 2. Getting started

### System requirements
- **Web browser:** Latest version of Chrome or Edge  
- **Network:** Stable internet or VPN access  
- **File formats:** .csv, .xlsx  

### Access the tool
1. Open your browser.  
2. Click GRS Tool URL.  
3. Enter your username and password.  
4. Select **Sign in**.  

**Note:** If you can't sign in, contact the IT administrator to reset your credentials.  
- Phone: XXXXXXXXXX  
- OR  
- Email: IT administrator  

### First-time setup
- Change your temporary password after you sign in.  
- Confirm your assigned role (Configuration, QA, or Admin).  

---

## 3. GRS Tool overview

The GRS Tool validates and manages healthcare design log records before they load into Facets.

### Key features
- Upload and validate design logs  
- Highlight errors automatically  
- Edit or delete invalid records  
- Migrate validated data across environments:  
  1. 1D (Development)  
  2. 1R (QA)  
  3. 1P (Production)  

### Navigation
- **Dashboard:** View log summaries and validation status  
- **Upload Design Log:** Upload a new file  
- **Validation table:** View valid and invalid records  
- **Migration panel:** Move records to other environments  

---

## 4. Working with the GRS Tool

### 4.1. Upload design logs
1. Sign in to the GRS Tool.  
2. On the dashboard, select **Upload Design Log**.  
3. In the dialog box, select **Browse**.  
4. Select a .csv or .xlsx file.  
5. Select **Upload**.  
6. Review the results in the Validation table.  
   - Valid records appear in black text  
   - Invalid records appear in red  

**Tip:** Upload only one log at a time. Large files may take longer to process.  

### 4.2. Validate records
1. Review the Validation table.  
2. Check records marked in red.  
3. Read the error message in the status column.  
   - Example: Invalid termination date  
4. Correct the error manually or delete the record.  
   - All corrected records appear in black text  

### 4.3. Edit records
1. In the Validation table, find the record in red.  
2. Select the **Edit** icon.  
3. Update the incorrect fields, such as:  
   - Age group  
   - Service code  
   - Termination date  
4. Select **Save**.  

**Note:** Only users with the Editor role can make updates.  

### 4.4. Delete records
1. In the Validation table, find the invalid record.  
2. Select the **Delete** icon.  
3. In the confirmation dialog, select **Yes**.  

**Warning:** Deleted records can't be recovered. Download a backup before you delete records.  

### 4.5. Migrate records
1. Confirm that all records in the Validation table are valid.  
2. In the Migration panel, select the target environment:  
   - 1D to 1R  
   - 1R to 1P  
3. Select **Migrate**.  
4. Wait for the migration confirmation message.  

**Note:** Only Admin role users can migrate records.  

---

## 5. Troubleshooting

| Problem           | Cause                  | Solution                       |
|------------------|-----------------------|--------------------------------|
| File upload fails | Unsupported format     | Upload a .csv or .xlsx file   |
| Record stays red  | Invalid field value    | Edit the field and save       |
| Migration blocked | Insufficient permissions | Contact an Admin             |
| Timeout error     | File is too large      | Split the file and re-upload  |

---

## 6. Best practices
- Validate all records before migration  
- Keep version history of design logs  
- Remove duplicates to reduce errors  
- Test migration in 1R before 1P  

---

## 7. Glossary
- **1D:** Development environment  
- **1R:** QA environment  
- **1P:** Production environment  
- **Design log:** File containing plan or service configurations  
- **Validation table:** Area that displays processed records  
- **Facets:** Healthcare administration platform  

---

## 8. Appendices
- **Related documents:** Installation Guide  
- **References:** SharePoint  

---

## 9. Document control

| Version | Date       | Author           | Changes          |
|---------|------------|----------------|----------------|
| v1.0    | 20-09-2025 | Rama Krishna K  | Initial release |
```


