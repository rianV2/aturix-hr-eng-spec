# HRIS MVP - Entity Relationship Diagram

This ERD contains only the essential tables required for the HRIS, focusing on core functionality for employee management, attendance, leave, payroll, and approval workflows.

## Mermaid ERD Diagram

```mermaid
erDiagram
    %% MASTER DATA
    COMPANIES ||--o{ DEPARTMENTS : has
    COMPANIES ||--o{ BRANCHES : has
    COMPANIES ||--o{ JOB_LEVELS : defines
    COMPANIES ||--o{ COMPANY_POLICIES : has
    COMPANIES ||--o{ LEAVE_TYPES : defines

    DEPARTMENTS ||--o{ POSITIONS : has
    DEPARTMENTS ||--o{ EMPLOYEES : contains

    BRANCHES ||--o{ EMPLOYEES : locates

    POSITIONS ||--o{ EMPLOYEES : assigned_to
    POSITIONS ||--o{ JOB_DESCRIPTIONS : has
    POSITIONS ||--o{ SALARY_STRUCTURES : has

    JOB_LEVELS ||--o{ POSITIONS : categorizes

    %% EMPLOYEE MANAGEMENT
    EMPLOYEES ||--o{ EMPLOYEE_CONTRACTS : has
    EMPLOYEES ||--o{ EMPLOYEE_FAMILIES : has
    EMPLOYEES ||--o{ EMPLOYEE_EDUCATIONS : has
    EMPLOYEES ||--o{ EMPLOYEE_EXPERIENCES : has
    EMPLOYEES ||--o{ EMPLOYEE_DOCUMENTS : has
    EMPLOYEES ||--o{ EMPLOYEE_COMPETENCIES : has
    EMPLOYEES ||--o{ BANK_ACCOUNTS : has

    %% ATTENDANCE & TIME MANAGEMENT
    EMPLOYEES ||--o{ ATTENDANCES : records
    EMPLOYEES ||--o{ OVERTIME_REQUESTS : submits

    %% LEAVE MANAGEMENT
    EMPLOYEES ||--o{ LEAVE_BALANCES : has
    EMPLOYEES ||--o{ LEAVE_REQUESTS : submits

    LEAVE_TYPES ||--o{ LEAVE_BALANCES : categorizes
    LEAVE_TYPES ||--o{ LEAVE_REQUESTS : categorizes
    LEAVE_TYPES ||--o{ LEAVE_POLICIES : has

    %% PAYROLL
    EMPLOYEES ||--o{ PAYROLL_RECORDS : receives
    EMPLOYEES ||--o{ SALARY_ADJUSTMENTS : has
    EMPLOYEES ||--o{ LOAN_REQUESTS : submits
    EMPLOYEES ||--o{ REIMBURSEMENTS : claims

    PAYROLL_RECORDS ||--o{ PAYROLL_COMPONENTS : contains
    PAYROLL_RECORDS ||--o{ PAYROLL_DEDUCTIONS : has

    PAYROLL_COMPONENTS }o--|| COMPONENT_TYPES : uses
    PAYROLL_DEDUCTIONS }o--|| DEDUCTION_TYPES : uses

    SALARY_STRUCTURES ||--o{ SALARY_COMPONENTS : defines

    %% APPROVAL WORKFLOWS
    APPROVAL_WORKFLOWS ||--o{ APPROVAL_STEPS : defines
    APPROVAL_STEPS ||--o{ APPROVAL_LOGS : tracks

    LEAVE_REQUESTS ||--o{ APPROVAL_LOGS : requires
    OVERTIME_REQUESTS ||--o{ APPROVAL_LOGS : requires
    LOAN_REQUESTS ||--o{ APPROVAL_LOGS : requires
    REIMBURSEMENTS ||--o{ APPROVAL_LOGS : requires

    EMPLOYEES ||--o{ APPROVAL_LOGS : approves

    %% ORGANIZATIONAL
    EMPLOYEES }o--|| EMPLOYEES : reports_to

    %% ANNOUNCEMENT & COMMUNICATION
    ANNOUNCEMENTS ||--o{ ANNOUNCEMENT_READS : tracked_by
    EMPLOYEES ||--o{ ANNOUNCEMENT_READS : reads

    COMPANIES {
        int id PK
        string company_name
        string company_code
        string tax_id
        string address
        string phone
        string email
        date established_date
        boolean is_active
    }

    BRANCHES {
        int id PK
        int company_id FK
        string branch_name
        string branch_code
        string address
        string city
        string province
        string postal_code
        string phone
        boolean is_head_office
        boolean is_active
    }

    DEPARTMENTS {
        int id PK
        int company_id FK
        int parent_department_id FK
        string department_name
        string department_code
        int head_employee_id FK
        boolean is_active
    }

    POSITIONS {
        int id PK
        int department_id FK
        int job_level_id FK
        string position_name
        string position_code
        int reports_to_position_id FK
        decimal min_salary
        decimal max_salary
        boolean is_active
    }

    JOB_LEVELS {
        int id PK
        int company_id FK
        string level_name
        int level_order
        string description
    }

    JOB_DESCRIPTIONS {
        int id PK
        int position_id FK
        text responsibilities
        text requirements
        text qualifications
        date effective_date
        boolean is_active
    }

    EMPLOYEES {
        int id PK
        string employee_number UK
        string first_name
        string last_name
        string email UK
        string phone
        date birth_date
        string gender
        string marital_status
        string id_number
        string tax_id
        int position_id FK
        int department_id FK
        int branch_id FK
        int manager_id FK
        string employment_status
        date join_date
        date permanent_date
        date resignation_date
        string photo_url
        boolean is_active
    }

    EMPLOYEE_CONTRACTS {
        int id PK
        int employee_id FK
        string contract_type
        date start_date
        date end_date
        decimal salary_amount
        text terms_conditions
        string status
    }

    EMPLOYEE_FAMILIES {
        int id PK
        int employee_id FK
        string relationship
        string full_name
        date birth_date
        string id_number
        boolean is_emergency_contact
    }

    EMPLOYEE_EDUCATIONS {
        int id PK
        int employee_id FK
        string degree_level
        string institution
        string major
        int graduation_year
        string certificate_url
    }

    EMPLOYEE_EXPERIENCES {
        int id PK
        int employee_id FK
        string company_name
        string position
        date start_date
        date end_date
        text job_description
    }

    EMPLOYEE_DOCUMENTS {
        int id PK
        int employee_id FK
        string document_type
        string document_name
        string file_url
        date upload_date
        date expiry_date
    }

    EMPLOYEE_COMPETENCIES {
        int id PK
        int employee_id FK
        string competency_name
        string competency_type
        int proficiency_level
        date assessment_date
    }

    BANK_ACCOUNTS {
        int id PK
        int employee_id FK
        string bank_name
        string account_number
        string account_holder_name
        boolean is_primary
    }

    ATTENDANCES {
        int id PK
        int employee_id FK
        date attendance_date
        time check_in
        time check_out
        string location_in
        string location_out
        string status
        text notes
    }

    OVERTIME_REQUESTS {
        int id PK
        int employee_id FK
        date overtime_date
        time start_time
        time end_time
        decimal hours
        text reason
        string status
        datetime created_at
    }

    LEAVE_TYPES {
        int id PK
        int company_id FK
        string leave_name
        int annual_quota
        boolean is_paid
        boolean requires_document
        int max_consecutive_days
        boolean is_active
    }

    LEAVE_POLICIES {
        int id PK
        int leave_type_id FK
        text policy_rules
        int notice_period_days
        date effective_date
    }

    LEAVE_BALANCES {
        int id PK
        int employee_id FK
        int leave_type_id FK
        int year
        int entitled
        int used
        int remaining
    }

    LEAVE_REQUESTS {
        int id PK
        int employee_id FK
        int leave_type_id FK
        date start_date
        date end_date
        decimal total_days
        text reason
        string document_url
        string status
        datetime created_at
    }

    SALARY_STRUCTURES {
        int id PK
        int position_id FK
        decimal base_salary
        date effective_date
        boolean is_active
    }

    SALARY_COMPONENTS {
        int id PK
        int structure_id FK
        int component_type_id FK
        string component_name
        decimal amount
        string calculation_type
        boolean is_taxable
    }

    COMPONENT_TYPES {
        int id PK
        string type_name
        string category
        boolean is_recurring
    }

    PAYROLL_RECORDS {
        int id PK
        int employee_id FK
        int period_month
        int period_year
        date payment_date
        decimal gross_salary
        decimal total_deductions
        decimal net_salary
        string status
        datetime processed_at
    }

    PAYROLL_COMPONENTS {
        int id PK
        int payroll_id FK
        int component_type_id FK
        string component_name
        decimal amount
        boolean is_taxable
    }

    PAYROLL_DEDUCTIONS {
        int id PK
        int payroll_id FK
        int deduction_type_id FK
        string deduction_name
        decimal amount
    }

    DEDUCTION_TYPES {
        int id PK
        string type_name
        string category
        boolean is_mandatory
    }

    SALARY_ADJUSTMENTS {
        int id PK
        int employee_id FK
        date effective_date
        decimal old_salary
        decimal new_salary
        decimal adjustment_percentage
        string adjustment_type
        text reason
        int approved_by FK
    }

    LOAN_REQUESTS {
        int id PK
        int employee_id FK
        decimal loan_amount
        int installment_months
        decimal installment_amount
        date start_date
        decimal outstanding_balance
        string status
        text purpose
        datetime created_at
    }

    REIMBURSEMENTS {
        int id PK
        int employee_id FK
        string reimbursement_type
        date expense_date
        decimal amount
        text description
        string receipt_url
        string status
        datetime created_at
    }

    APPROVAL_WORKFLOWS {
        int id PK
        int company_id FK
        string workflow_name
        string entity_type
        text conditions
        boolean is_active
    }

    APPROVAL_STEPS {
        int id PK
        int workflow_id FK
        int step_order
        string approver_type
        int approver_id FK
        boolean is_required
        int escalation_hours
    }

    APPROVAL_LOGS {
        int id PK
        int workflow_id FK
        int step_id FK
        string entity_type
        int entity_id
        int approver_id FK
        string action
        text comments
        datetime action_datetime
    }

    ANNOUNCEMENTS {
        int id PK
        int company_id FK
        string title
        text content
        string priority
        date publish_date
        date expiry_date
        int created_by FK
        boolean is_active
    }

    ANNOUNCEMENT_READS {
        int id PK
        int announcement_id FK
        int employee_id FK
        datetime read_at
    }

    COMPANY_POLICIES {
        int id PK
        int company_id FK
        string policy_name
        string policy_category
        text policy_content
        date effective_date
        int version
        boolean is_active
    }
```