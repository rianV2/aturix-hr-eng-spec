# HRIS API Contract

## Overview

This document defines the REST API contract for the HRIS web frontend back office integration.

- **API Version**: v1
- **Base URL**: `https://api.hris.example.com/api/v1`
- **Authentication**: Bearer Token
- **Content Type**: `application/json`

---

## Authentication & Headers

### Request Headers

```http
Authorization: Bearer {access_token}
Content-Type: application/json
Accept: application/json
X-Company-ID: {company_id}
```

### Response Headers

```http
Content-Type: application/json
X-Request-ID: {unique_request_id}
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640995200
```

---

## HTTP Methods

| Method | Purpose | Usage |
|--------|---------|-------|
| `GET` | Retrieve data | List resources or get single resource |
| `POST` | Create data | Create new resource |
| `PATCH` | Update data | Partially update existing resource |
| `DELETE` | Remove data | Delete resource |

---

## Pagination

The API supports two pagination modes:

### 1. Cursor-Based Pagination (Recommended)

**Request:**
```http
GET /api/v1/employees?last_id=550e8400-e29b-41d4-a716-446655440000&limit=20
```

**Benefits:**
- Better performance for large datasets
- Consistent results during real-time updates
- No page drift issues

### 2. Page-Based Pagination (Traditional)

**Request:**
```http
GET /api/v1/employees?page=2&limit=20
```

**Usage:**
- Used when `last_id` parameter is omitted
- Simple pagination for smaller datasets

**Pagination Response:**
```json
"_meta": {
  "pagination": {
    "total": 1000,
    "count": 20,
    "per_page": 20,
    "current_page": 2,
    "total_pages": 50,
    "next_cursor": "660e8400-e29b-41d4-a716-446655440001",
    "has_more": true
  }
}
```

**Fields:**
- `total`: Total number of records
- `count`: Number of records in current response
- `per_page`: Records per page/request
- `current_page`: Current page number (page-based only)
- `total_pages`: Total pages (page-based only)
- `next_cursor`: The `last_id` value to use for the next cursor-based request
- `has_more`: Boolean indicating if more records exist

---

## Query Parameters

### Filtering
```http
GET /api/v1/employees?filter[status]=active&filter[department]=engineering
```

### Sorting
```http
GET /api/v1/employees?sort=-created_at,name
```
- Use `-` prefix for descending order
- Comma-separated for multiple fields

### Search
```http
GET /api/v1/employees?search=john
```
- Full-text search across multiple fields

### Field Selection (Sparse Fieldsets)
```http
GET /api/v1/employees?fields=id,name,email,department
```
- Return only specified fields
- Reduces response size

### Including Relations
```http
GET /api/v1/employees?include=department,position,contracts
```
- Include related resources in response
- Reduces number of API calls

---

## Standard Response Format

### Success Response (List)

```json
{
  "data": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "employee_number": "EMP-001",
      "name": "John Doe",
      "email": "john.doe@example.com",
      "status": "active",
      "created_at": "2024-01-15T08:30:00Z",
      "updated_at": "2024-01-15T08:30:00Z"
    }
  ],
  "_meta": {
    "pagination": {
      "total": 1000,
      "count": 20,
      "per_page": 20,
      "current_page": 1,
      "total_pages": 50,
      "next_cursor": "660e8400-e29b-41d4-a716-446655440001",
      "has_more": true
    }
  },
  "_filter": {
    "status": [
      {"id": "active", "name": "Active"},
      {"id": "inactive", "name": "Inactive"},
      {"id": "suspended", "name": "Suspended"}
    ],
    "department": [
      {"id": 1, "name": "Engineering"},
      {"id": 2, "name": "Human Resources"},
      {"id": 3, "name": "Finance"}
    ],
    "position": [
      {"id": 1, "name": "Software Engineer"},
      {"id": 2, "name": "HR Manager"},
      {"id": 3, "name": "Accountant"}
    ]
  }
}
```

### Success Response (Single Resource)

```json
{
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "employee_number": "EMP-001",
    "name": "John Doe",
    "email": "john.doe@example.com",
    "phone": "+62812345678",
    "status": "active",
    "department": {
      "id": 1,
      "name": "Engineering"
    },
    "position": {
      "id": 1,
      "name": "Software Engineer"
    },
    "created_at": "2024-01-15T08:30:00Z",
    "updated_at": "2024-01-15T08:30:00Z"
  }
}
```

### Error Response

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed for one or more fields",
    "details": [
      {
        "field": "email",
        "message": "Email format is invalid"
      },
      {
        "field": "phone",
        "message": "Phone number is required"
      }
    ]
  }
}
```

### The `_filter` Object

The `_filter` object provides available filter options for the current resource, enabling frontends to build dynamic filter UI components.

**Structure:**
- Each key represents a filterable field
- Values are arrays of objects with `id` and `name` properties
- Options reflect actual available values in the dataset

**Example Usage:**
```javascript
// Frontend can use _filter to populate dropdown filters
const statusOptions = response._filter.status;
// [{"id": "active", "name": "Active"}, {"id": "inactive", "name": "Inactive"}]
```

---

## HTTP Status Codes

| Status Code | Description | Usage |
|-------------|-------------|-------|
| `200 OK` | Success | GET, PATCH, DELETE operations succeeded |
| `201 Created` | Resource created | POST operation succeeded |
| `400 Bad Request` | Invalid request | Malformed request or invalid parameters |
| `401 Unauthorized` | Authentication required | Missing or invalid token |
| `403 Forbidden` | Access denied | User lacks permission for resource |
| `404 Not Found` | Resource not found | Resource with given ID doesn't exist |
| `422 Unprocessable Entity` | Business logic error | Validation failed or business rule violated |
| `429 Too Many Requests` | Rate limit exceeded | Too many requests in time window |
| `500 Internal Server Error` | Server error | Unexpected server-side error |

---

## Complete Example: Employees Resource

### Base Endpoint
```
/api/v1/employees
```

---

### 1. GET List (Cursor-Based Pagination)

**Request:**
```http
GET /api/v1/employees?last_id=550e8400-e29b-41d4-a716-446655440000&limit=20&filter[status]=active&sort=-created_at
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
X-Company-ID: 123
```

**Response:** `200 OK`
```json
{
  "data": [
    {
      "id": "660e8400-e29b-41d4-a716-446655440001",
      "employee_number": "EMP-002",
      "first_name": "Jane",
      "last_name": "Smith",
      "email": "jane.smith@example.com",
      "phone": "+62812345679",
      "status": "active",
      "hire_date": "2024-01-01",
      "department_id": 1,
      "position_id": 2,
      "created_at": "2024-01-10T10:00:00Z",
      "updated_at": "2024-01-10T10:00:00Z"
    },
    {
      "id": "770e8400-e29b-41d4-a716-446655440002",
      "employee_number": "EMP-003",
      "first_name": "Bob",
      "last_name": "Johnson",
      "email": "bob.johnson@example.com",
      "phone": "+62812345680",
      "status": "active",
      "hire_date": "2024-01-05",
      "department_id": 2,
      "position_id": 3,
      "created_at": "2024-01-09T14:30:00Z",
      "updated_at": "2024-01-09T14:30:00Z"
    }
  ],
  "_meta": {
    "pagination": {
      "total": 156,
      "count": 20,
      "per_page": 20,
      "current_page": null,
      "total_pages": null,
      "next_cursor": "770e8400-e29b-41d4-a716-446655440002",
      "has_more": true
    }
  },
  "_filter": {
    "status": [
      {"id": "active", "name": "Active"},
      {"id": "inactive", "name": "Inactive"},
      {"id": "suspended", "name": "Suspended"}
    ],
    "department": [
      {"id": 1, "name": "Engineering"},
      {"id": 2, "name": "Human Resources"},
      {"id": 3, "name": "Finance"},
      {"id": 4, "name": "Marketing"}
    ],
    "position": [
      {"id": 1, "name": "Junior Developer"},
      {"id": 2, "name": "Senior Developer"},
      {"id": 3, "name": "HR Manager"}
    ],
    "employment_type": [
      {"id": "full_time", "name": "Full Time"},
      {"id": "part_time", "name": "Part Time"},
      {"id": "contract", "name": "Contract"}
    ]
  }
}
```

---

### 2. GET List (Page-Based Pagination)

**Request:**
```http
GET /api/v1/employees?page=2&limit=20&filter[department]=engineering&search=john
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
X-Company-ID: 123
```

**Response:** `200 OK`
```json
{
  "data": [
    {
      "id": "880e8400-e29b-41d4-a716-446655440003",
      "employee_number": "EMP-025",
      "first_name": "John",
      "last_name": "Doe",
      "email": "john.doe@example.com",
      "phone": "+62812345681",
      "status": "active",
      "hire_date": "2023-12-15",
      "department_id": 1,
      "position_id": 1,
      "created_at": "2023-12-15T09:00:00Z",
      "updated_at": "2024-01-10T11:00:00Z"
    }
  ],
  "_meta": {
    "pagination": {
      "total": 45,
      "count": 20,
      "per_page": 20,
      "current_page": 2,
      "total_pages": 3,
      "next_cursor": "880e8400-e29b-41d4-a716-446655440003",
      "has_more": true
    }
  },
  "_filter": {
    "status": [
      {"id": "active", "name": "Active"},
      {"id": "inactive", "name": "Inactive"}
    ],
    "department": [
      {"id": 1, "name": "Engineering"}
    ],
    "position": [
      {"id": 1, "name": "Junior Developer"},
      {"id": 2, "name": "Senior Developer"}
    ]
  }
}
```

---

### 3. GET Single Resource

**Request:**
```http
GET /api/v1/employees/550e8400-e29b-41d4-a716-446655440000?include=department,position,contracts
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
X-Company-ID: 123
```

**Response:** `200 OK`
```json
{
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "employee_number": "EMP-001",
    "first_name": "John",
    "last_name": "Doe",
    "email": "john.doe@example.com",
    "phone": "+62812345678",
    "date_of_birth": "1990-05-15",
    "gender": "male",
    "marital_status": "married",
    "address": "Jl. Sudirman No. 123, Jakarta",
    "city": "Jakarta",
    "province": "DKI Jakarta",
    "postal_code": "12345",
    "status": "active",
    "hire_date": "2023-01-15",
    "department_id": 1,
    "position_id": 2,
    "branch_id": 1,
    "manager_id": "440e8400-e29b-41d4-a716-446655440099",
    "created_at": "2023-01-10T08:00:00Z",
    "updated_at": "2024-01-15T10:30:00Z",
    "department": {
      "id": 1,
      "name": "Engineering",
      "code": "ENG"
    },
    "position": {
      "id": 2,
      "name": "Senior Software Engineer",
      "level": "Senior"
    },
    "contracts": [
      {
        "id": 1,
        "contract_type": "permanent",
        "start_date": "2023-01-15",
        "end_date": null,
        "is_active": true
      }
    ]
  }
}
```

---

### 4. POST Create Resource

**Request:**
```http
POST /api/v1/employees
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
X-Company-ID: 123
Content-Type: application/json

{
  "employee_number": "EMP-999",
  "first_name": "Alice",
  "last_name": "Williams",
  "email": "alice.williams@example.com",
  "phone": "+62812345999",
  "date_of_birth": "1995-03-20",
  "gender": "female",
  "marital_status": "single",
  "address": "Jl. Thamrin No. 456, Jakarta",
  "city": "Jakarta",
  "province": "DKI Jakarta",
  "postal_code": "10230",
  "hire_date": "2024-02-01",
  "department_id": 1,
  "position_id": 1,
  "branch_id": 1,
  "manager_id": "550e8400-e29b-41d4-a716-446655440000"
}
```

**Response:** `201 Created`
```json
{
  "data": {
    "id": "990e8400-e29b-41d4-a716-446655440999",
    "employee_number": "EMP-999",
    "first_name": "Alice",
    "last_name": "Williams",
    "email": "alice.williams@example.com",
    "phone": "+62812345999",
    "date_of_birth": "1995-03-20",
    "gender": "female",
    "marital_status": "single",
    "address": "Jl. Thamrin No. 456, Jakarta",
    "city": "Jakarta",
    "province": "DKI Jakarta",
    "postal_code": "10230",
    "status": "active",
    "hire_date": "2024-02-01",
    "department_id": 1,
    "position_id": 1,
    "branch_id": 1,
    "manager_id": "550e8400-e29b-41d4-a716-446655440000",
    "created_at": "2024-01-20T15:30:00Z",
    "updated_at": "2024-01-20T15:30:00Z"
  }
}
```

---

### 5. PATCH Update Resource (Partial)

**Request:**
```http
PATCH /api/v1/employees/550e8400-e29b-41d4-a716-446655440000
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
X-Company-ID: 123
Content-Type: application/json

{
  "phone": "+62812999999",
  "position_id": 3,
  "address": "Jl. Gatot Subroto No. 789, Jakarta"
}
```

**Response:** `200 OK`
```json
{
  "data": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "employee_number": "EMP-001",
    "first_name": "John",
    "last_name": "Doe",
    "email": "john.doe@example.com",
    "phone": "+62812999999",
    "date_of_birth": "1990-05-15",
    "gender": "male",
    "marital_status": "married",
    "address": "Jl. Gatot Subroto No. 789, Jakarta",
    "city": "Jakarta",
    "province": "DKI Jakarta",
    "postal_code": "12345",
    "status": "active",
    "hire_date": "2023-01-15",
    "department_id": 1,
    "position_id": 3,
    "branch_id": 1,
    "manager_id": "440e8400-e29b-41d4-a716-446655440099",
    "created_at": "2023-01-10T08:00:00Z",
    "updated_at": "2024-01-20T16:00:00Z"
  }
}
```

---

### 6. DELETE Resource

**Request:**
```http
DELETE /api/v1/employees/550e8400-e29b-41d4-a716-446655440000
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
X-Company-ID: 123
```

**Response:** `200 OK`
```json
{
  "data": {
    "message": "Employee deleted successfully",
    "id": "550e8400-e29b-41d4-a716-446655440000"
  }
}
```

---

### 7. Error Examples

#### Validation Error

**Request:**
```http
POST /api/v1/employees
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
X-Company-ID: 123
Content-Type: application/json

{
  "first_name": "Alice",
  "email": "invalid-email",
  "phone": "123"
}
```

**Response:** `422 Unprocessable Entity`
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed for one or more fields",
    "details": [
      {
        "field": "email",
        "message": "Email format is invalid"
      },
      {
        "field": "last_name",
        "message": "Last name is required"
      },
      {
        "field": "department_id",
        "message": "Department is required"
      }
    ]
  }
}
```

#### Not Found Error

**Request:**
```http
GET /api/v1/employees/999e8400-e29b-41d4-a716-446655440000
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
X-Company-ID: 123
```

**Response:** `404 Not Found`
```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Employee with ID 999e8400-e29b-41d4-a716-446655440000 not found",
    "details": []
  }
}
```

#### Unauthorized Error

**Request:**
```http
GET /api/v1/employees
X-Company-ID: 123
```

**Response:** `401 Unauthorized`
```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Authentication token is missing or invalid",
    "details": []
  }
}
```

#### Forbidden Error

**Request:**
```http
DELETE /api/v1/employees/550e8400-e29b-41d4-a716-446655440000
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
X-Company-ID: 123
```

**Response:** `403 Forbidden`
```json
{
  "error": {
    "code": "FORBIDDEN",
    "message": "You do not have permission to delete this employee",
    "details": []
  }
}
```

---

## All Resource Endpoints

The API follows the same pattern for all resources. Replace `{resource}` with the specific resource name.

### Master Data

```
GET    /api/v1/companies
GET    /api/v1/companies/{id}
POST   /api/v1/companies
PATCH  /api/v1/companies/{id}
DELETE /api/v1/companies/{id}

GET    /api/v1/branches
GET    /api/v1/branches/{id}
POST   /api/v1/branches
PATCH  /api/v1/branches/{id}
DELETE /api/v1/branches/{id}

GET    /api/v1/departments
GET    /api/v1/departments/{id}
POST   /api/v1/departments
PATCH  /api/v1/departments/{id}
DELETE /api/v1/departments/{id}

GET    /api/v1/positions
GET    /api/v1/positions/{id}
POST   /api/v1/positions
PATCH  /api/v1/positions/{id}
DELETE /api/v1/positions/{id}

GET    /api/v1/job-levels
GET    /api/v1/job-levels/{id}
POST   /api/v1/job-levels
PATCH  /api/v1/job-levels/{id}
DELETE /api/v1/job-levels/{id}

GET    /api/v1/company-policies
GET    /api/v1/company-policies/{id}
POST   /api/v1/company-policies
PATCH  /api/v1/company-policies/{id}
DELETE /api/v1/company-policies/{id}
```

### Employee Management

```
GET    /api/v1/employees
GET    /api/v1/employees/{id}
POST   /api/v1/employees
PATCH  /api/v1/employees/{id}
DELETE /api/v1/employees/{id}

GET    /api/v1/employees/{id}/contracts
GET    /api/v1/employees/{id}/contracts/{contract_id}
POST   /api/v1/employees/{id}/contracts
PATCH  /api/v1/employees/{id}/contracts/{contract_id}
DELETE /api/v1/employees/{id}/contracts/{contract_id}

GET    /api/v1/employees/{id}/families
GET    /api/v1/employees/{id}/families/{family_id}
POST   /api/v1/employees/{id}/families
PATCH  /api/v1/employees/{id}/families/{family_id}
DELETE /api/v1/employees/{id}/families/{family_id}

GET    /api/v1/employees/{id}/educations
GET    /api/v1/employees/{id}/educations/{education_id}
POST   /api/v1/employees/{id}/educations
PATCH  /api/v1/employees/{id}/educations/{education_id}
DELETE /api/v1/employees/{id}/educations/{education_id}

GET    /api/v1/employees/{id}/experiences
GET    /api/v1/employees/{id}/experiences/{experience_id}
POST   /api/v1/employees/{id}/experiences
PATCH  /api/v1/employees/{id}/experiences/{experience_id}
DELETE /api/v1/employees/{id}/experiences/{experience_id}

GET    /api/v1/employees/{id}/documents
GET    /api/v1/employees/{id}/documents/{document_id}
POST   /api/v1/employees/{id}/documents
PATCH  /api/v1/employees/{id}/documents/{document_id}
DELETE /api/v1/employees/{id}/documents/{document_id}

GET    /api/v1/employees/{id}/bank-accounts
GET    /api/v1/employees/{id}/bank-accounts/{account_id}
POST   /api/v1/employees/{id}/bank-accounts
PATCH  /api/v1/employees/{id}/bank-accounts/{account_id}
DELETE /api/v1/employees/{id}/bank-accounts/{account_id}
```

### Attendance & Time Management

```
GET    /api/v1/attendances
GET    /api/v1/attendances/{id}
POST   /api/v1/attendances
PATCH  /api/v1/attendances/{id}
DELETE /api/v1/attendances/{id}

GET    /api/v1/overtime-requests
GET    /api/v1/overtime-requests/{id}
POST   /api/v1/overtime-requests
PATCH  /api/v1/overtime-requests/{id}
DELETE /api/v1/overtime-requests/{id}
```

### Leave Management

```
GET    /api/v1/leave-types
GET    /api/v1/leave-types/{id}
POST   /api/v1/leave-types
PATCH  /api/v1/leave-types/{id}
DELETE /api/v1/leave-types/{id}

GET    /api/v1/leave-policies
GET    /api/v1/leave-policies/{id}
POST   /api/v1/leave-policies
PATCH  /api/v1/leave-policies/{id}
DELETE /api/v1/leave-policies/{id}

GET    /api/v1/leave-balances
GET    /api/v1/leave-balances/{id}
POST   /api/v1/leave-balances
PATCH  /api/v1/leave-balances/{id}
DELETE /api/v1/leave-balances/{id}

GET    /api/v1/leave-requests
GET    /api/v1/leave-requests/{id}
POST   /api/v1/leave-requests
PATCH  /api/v1/leave-requests/{id}
DELETE /api/v1/leave-requests/{id}
```

### Payroll & Compensation

```
GET    /api/v1/salary-structures
GET    /api/v1/salary-structures/{id}
POST   /api/v1/salary-structures
PATCH  /api/v1/salary-structures/{id}
DELETE /api/v1/salary-structures/{id}

GET    /api/v1/salary-components
GET    /api/v1/salary-components/{id}
POST   /api/v1/salary-components
PATCH  /api/v1/salary-components/{id}
DELETE /api/v1/salary-components/{id}

GET    /api/v1/payroll-records
GET    /api/v1/payroll-records/{id}
POST   /api/v1/payroll-records
PATCH  /api/v1/payroll-records/{id}
DELETE /api/v1/payroll-records/{id}

GET    /api/v1/salary-adjustments
GET    /api/v1/salary-adjustments/{id}
POST   /api/v1/salary-adjustments
PATCH  /api/v1/salary-adjustments/{id}
DELETE /api/v1/salary-adjustments/{id}

GET    /api/v1/reimbursements
GET    /api/v1/reimbursements/{id}
POST   /api/v1/reimbursements
PATCH  /api/v1/reimbursements/{id}
DELETE /api/v1/reimbursements/{id}

GET    /api/v1/loan-requests
GET    /api/v1/loan-requests/{id}
POST   /api/v1/loan-requests
PATCH  /api/v1/loan-requests/{id}
DELETE /api/v1/loan-requests/{id}
```

### Approval Workflows

```
GET    /api/v1/approval-workflows
GET    /api/v1/approval-workflows/{id}
POST   /api/v1/approval-workflows
PATCH  /api/v1/approval-workflows/{id}
DELETE /api/v1/approval-workflows/{id}

GET    /api/v1/approval-logs
GET    /api/v1/approval-logs/{id}
POST   /api/v1/approval-logs
PATCH  /api/v1/approval-logs/{id}
DELETE /api/v1/approval-logs/{id}
```

### Communication

```
GET    /api/v1/announcements
GET    /api/v1/announcements/{id}
POST   /api/v1/announcements
PATCH  /api/v1/announcements/{id}
DELETE /api/v1/announcements/{id}
```

---

## Additional Features

### Bulk Operations

#### Bulk Import
```http
POST /api/v1/employees/bulk-import
Content-Type: multipart/form-data

{
  "file": employees.csv
}
```

#### Bulk Update
```http
PATCH /api/v1/employees/bulk-update
Content-Type: application/json

{
  "ids": ["id1", "id2", "id3"],
  "data": {
    "status": "inactive"
  }
}
```

#### Bulk Delete
```http
DELETE /api/v1/employees/bulk-delete
Content-Type: application/json

{
  "ids": ["id1", "id2", "id3"]
}
```

### Export Operations

```http
GET /api/v1/employees/export?format=csv&filter[department]=engineering&fields=name,email,phone
Authorization: Bearer {token}
X-Company-ID: 123

Response: CSV file download or
{
  "data": {
    "export_id": "export-123",
    "status": "processing",
    "download_url": null,
    "message": "Export job queued. Check status at /api/v1/exports/export-123"
  }
}
```

**Supported Formats:**
- `csv`
- `xlsx` (Excel)
- `pdf`

### Action Endpoints (Approvals)

```http
POST /api/v1/leave-requests/{id}/approve
Content-Type: application/json

{
  "notes": "Approved by manager",
  "approved_at": "2024-01-20T10:00:00Z"
}

POST /api/v1/leave-requests/{id}/reject
Content-Type: application/json

{
  "reason": "Insufficient leave balance",
  "rejected_at": "2024-01-20T10:00:00Z"
}
```

---

## Best Practices

### Naming Conventions
- Use kebab-case for URLs: `/leave-requests`, `/employee-documents`
- Use snake_case for JSON fields: `first_name`, `created_at`
- Use plural nouns for resource names: `/employees`, `/departments`

### Date & Time Format
- Use ISO 8601 format: `2024-01-20T15:30:00Z`
- All timestamps in UTC timezone
- Date-only fields: `2024-01-20`

### Multi-Tenancy
- Always include `X-Company-ID` header for multi-tenant operations
- API automatically filters data based on company context

### Rate Limiting
- Default limit: 1000 requests per hour per user
- Check `X-RateLimit-*` headers in responses
- Implement exponential backoff when rate limited

### Idempotency
- Use `Idempotency-Key` header for POST/PATCH operations to prevent duplicate requests
```http
POST /api/v1/employees
Idempotency-Key: 550e8400-e29b-41d4-a716-446655440000
```

### Versioning
- API version specified in URL: `/api/v1/`
- Breaking changes will increment major version: `/api/v2/`
- Non-breaking changes (new fields, new endpoints) added to existing version

---

## Common Error Codes

| Code | Description |
|------|-------------|
| `VALIDATION_ERROR` | Request validation failed |
| `AUTHENTICATION_ERROR` | Authentication failed or token invalid |
| `AUTHORIZATION_ERROR` | User lacks permission for action |
| `RESOURCE_NOT_FOUND` | Requested resource doesn't exist |
| `DUPLICATE_ENTRY` | Resource with unique field already exists |
| `BUSINESS_RULE_VIOLATION` | Operation violates business logic |
| `RATE_LIMIT_EXCEEDED` | Too many requests |
| `INTERNAL_SERVER_ERROR` | Unexpected server error |

---

## Support & Documentation

- **API Documentation**: https://api.hris.example.com/docs
- **Postman Collection**: https://api.hris.example.com/postman
- **OpenAPI Spec**: https://api.hris.example.com/openapi.json
- **Support Email**: api-support@hris.example.com
