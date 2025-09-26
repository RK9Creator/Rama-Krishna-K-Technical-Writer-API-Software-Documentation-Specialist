# GRS (Group Retire Solution) Tool API Documentation
**Version:** 1.0 — **Last Updated:** September 25, 2025  
**Author:** Rama Krishna — Technical Writer  

---

## Overview
The GRS (Group Retire Solution) Tool API enables the Configuration Team to efficiently manage benefit configurations within the Facets system. It provides functionality to upload and process design logs (Excel/CSV), retrieve, edit, and delete individual records, validate data before committing, and load validated data into the Facets 1D Region.  

This API is intended for internal use and system-to-system integrations.

---

## Table of Contents
1. [Overview](#overview)
2. [Base URL & Authentication](#base-url--authentication)
3. [Error Codes](#error-codes)
4. [Endpoints (Full Reference)](#endpoints-full-reference)
   - [Upload Design Log](#upload-design-log)
   - [Get Records](#get-records)
   - [Edit Record](#edit-record)
   - [Delete Record](#delete-record)
   - [Validate Data](#validate-data)
   - [Load Data to Facets 1D Region](#load-data-to-facets-1d-region)
5. [CSV / Excel Template & Field Definitions](#csv--excel-template--field-definitions)
6. [Workflow](#typical-workflow)
7. [Postman Quickstart](#postman-quickstart)
8. [Best Practices & Security](#best-practices--security)
9. [Troubleshooting & FAQ](#troubleshooting--faq)
10. [Contact & Changelog](#contact--changelog)

---

## Base URL & Authentication
**Base URL:**  
```

[https://grs-tool.company.com/api/v1](https://grs-tool.company.com/api/v1)

```

**Authentication:**  
The API uses Bearer Token Authentication. Every request must include the header:  
```

Authorization: Bearer <your_access_token>

```
- All traffic must use HTTPS.  
- Tokens must be stored securely and rotated regularly.  

---

## Error Codes

| HTTP Status Code | Meaning                         | Example Response (JSON)                   |
|-----------------|---------------------------------|-----------------------------------------|
| 200             | OK – Successful request          | `{ "status": "success" }`               |
| 201             | Created – Resource created       | `{ "upload_id": "UPLOAD12345" }`       |
| 400             | Bad Request – Invalid input      | `{ "error": "Invalid file format" }`   |
| 401             | Unauthorized – Missing/Invalid  | `{ "error": "Unauthorized" }`          |
| 404             | Not Found – Resource not found   | `{ "error": "Upload ID not found" }`   |
| 500             | Internal Server Error            | `{ "error": "Server error" }`          |

---

## Endpoints (Full Reference)

### Upload Design Log
**Description:** Uploads a design log file (CSV or Excel) to create records for validation and eventual loading into Facets.  

**Method:** `POST`  
**Endpoint:** `/upload-design-log`  

**Request Headers:**
```

Authorization: Bearer <your_access_token>
Content-Type: multipart/form-data

````

**Request (cURL):**
```bash
curl -X POST "https://grs-tool.company.com/api/v1/upload-design-log" \
-H "Authorization: Bearer <your_access_token>" \
-F "file=@/path/to/design_log.xlsx"
````

**Success Response (201 Created):**

```json
{
  "upload_id": "UPLOAD12345",
  "status": "success",
  "message": "Design log uploaded successfully"
}
```

**Notes:**

* Accepted file types: `.xlsx`, `.csv` (UTF-8 encoding).
* Server parses the file and creates records; use `upload_id` to manage records.
* Failing format validation returns a 400 error with details.

---

### Get Records

**Description:** Retrieves records created from an upload. Use the `upload_id` returned by the upload endpoint.

**Method:** `GET`
**Endpoint:** `/records/{upload_id}`

**Path Parameters:**

* `upload_id` (string) — unique ID returned by upload.

**Query Parameters (optional):**

* `page` (integer) — page number, default 1
* `limit` (integer) — page size, default 50

**Request Example:**

```
GET /records/UPLOAD12345?page=1&limit=50
```

**Response Example (200 OK):**

```json
{
  "upload_id": "UPLOAD12345",
  "page": 1,
  "limit": 50,
  "total_records": 1020,
  "records": [
    {
      "record_id": "REC001",
      "service_code": "SVC001",
      "termination_date": "2025-10-01",
      "age_group": "18-60",
      "status": "error",
      "error_message": "Missing service code"
    },
    {
      "record_id": "REC002",
      "service_code": "SVC002",
      "termination_date": "2025-12-31",
      "age_group": "18-65",
      "status": "valid",
      "error_message": null
    }
  ]
}
```

**Notes:**

* Use pagination for large results.
* Records with `status == error` include `error_message`.
* Fix issues using Edit or Delete endpoints, then re-run validation.

---

### Edit Record

**Description:** Update a single record created from an upload.

**Method:** `PUT`
**Endpoint:** `/record/{record_id}`

**Request Body (JSON):**

```json
{
  "service_code": "SVC999",
  "termination_date": "2025-12-31",
  "age_group": "18-65"
}
```

**Response Example (200 OK):**

```json
{
  "record_id": "REC001",
  "status": "updated",
  "message": "Record updated successfully"
}
```

**Notes:**

* API performs schema validation on updates.
* Invalid updates return 400 Bad Request with details.

---

### Delete Record

**Description:** Permanently deletes a record by ID.

**Method:** `DELETE`
**Endpoint:** `/record/{record_id}`

**Request Example:**

```
DELETE /record/REC001
```

**Response Example (200 OK):**

```json
{
  "record_id": "REC001",
  "status": "deleted",
  "message": "Record deleted successfully"
}
```

**Notes:**

* Deletion is permanent. Ensure correct `record_id`.

---

### Validate Data

**Description:** Runs validation checks across all records associated with an `upload_id`.

**Method:** `POST`
**Endpoint:** `/validate/{upload_id}`

**Request Example:**

```
POST /validate/UPLOAD12345
```

**Response Example (200 OK):**

```json
{
  "upload_id": "UPLOAD12345",
  "status": "validated",
  "errors_found": 10
}
```

**Notes:**

* `errors_found` indicates number of records with validation failures.
* Use `GET /records/{upload_id}` for detailed errors.
* Validation must succeed (`errors_found = 0`) before loading.

---

### Load Data to Facets 1D Region

**Description:** Loads validated data into the Facets 1D Region.

**Method:** `POST`
**Endpoint:** `/load/{upload_id}`

**Request Example:**

```
POST /load/UPLOAD12345
```

**Response Example (200 OK):**

```json
{
  "upload_id": "UPLOAD12345",
  "status": "loaded",
  "message": "Data loaded to 1D region successfully"
}
```

**Notes:**

* Loading is a final step; records are committed in Facets.
* 500 errors indicate system issues—contact the Configuration Team.

---

## CSV / Excel Template & Field Definitions

**Suggested CSV Header (UTF-8 encoded):**

```
service_code,plan_code,effective_date,termination_date,age_group,benefit_code,amount,notes
```

**Sample Row:**

```
SVC001,PLN001,2025-01-01,2025-12-31,18-65,BEN001,100.00,Annual benefit
```

**Field Definitions:**

| Field            | Type    | Required | Description                                 |
| ---------------- | ------- | -------- | ------------------------------------------- |
| service_code     | string  | Yes      | Unique code identifying the service/benefit |
| plan_code        | string  | Optional | Plan identifier (if applicable)             |
| effective_date   | date    | Optional | Start date for the benefit (YYYY-MM-DD)     |
| termination_date | date    | Optional | End date for the benefit (YYYY-MM-DD)       |
| age_group        | string  | Optional | Age eligibility, e.g., '18-65'              |
| benefit_code     | string  | Optional | Internal benefit code                       |
| amount           | decimal | Optional | Numeric amount when applicable              |
| notes            | string  | Optional | Free-text notes                             |

---

## Typical Workflow

1. Prepare design log (CSV/XLSX) using the template above.
2. `POST /upload-design-log` → receive `upload_id`.
3. `GET /records/{upload_id}` → review and fix errors.
4. Use `PUT /record/{record_id}` or `DELETE` to correct records.
5. `POST /validate/{upload_id}` → ensure `errors_found = 0`.
6. `POST /load/{upload_id}` to commit data to Facets 1D Region.

---

## Postman Quickstart

1. Create a new Collection named **GRS Tool API**.
2. Add an Environment with variables:

   * `base_url = https://grs-tool.company.com/api/v1`
   * `token = <your_access_token>`
3. Create requests for each endpoint using this document.
4. For upload, select Body → form-data and add key `file` (type: file).
5. Use tests to capture `upload_id` from upload response for chained testing.

---

## Best Practices & Security

* Use HTTPS for all requests; reject non-HTTPS traffic.
* Keep tokens secret and rotate periodically.
* Validate data locally before uploading to reduce server-side errors.
* Implement retry with exponential backoff for transient errors (5xx).
* Log API requests/responses (excluding sensitive tokens) for auditing.
* Enforce least privilege on tokens (scope access as needed).

---

## Troubleshooting & FAQ

**Q:** I received 401 Unauthorized. What should I check?
**A:** Verify Authorization header, token validity, expiry, and scope.

**Q:** What if my file fails to parse?
**A:** Ensure file format is `.xlsx` or `.csv` UTF-8, and headers match the template. Check `error_message` for row-level issues.

**Q:** Who should I contact for environment issues?
**A:** Contact the Configuration Team or API Owner in the internal directory.

---

## Contact & Changelog

* **Configuration Team / API Owner:** (refer to internal directory)
* **For urgent issues:** escalate via standard incident process.

**Version 1.0 — September 25, 2025**

* Initial complete documentation authored by Rama Krishna.



