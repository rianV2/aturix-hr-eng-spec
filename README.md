# Human Resource Information System (HRIS)

## Executive Summary

The HRIS provides integrated solutions for:
- **Employee Management**: Complete employee lifecycle from onboarding to offboarding
- **Attendance & Time Tracking**: GPS-based clock in/out with offline capability
- **Payroll & Compensation**: Automated salary processing with tax calculations and compliance
- **Self-Service Portal**: Employee self-service capabilities for profile management and requests
- **Approval Workflows**: Configurable approval matrix for leave, overtime, and reimbursements
- **Analytics & Reporting**: Comprehensive HR analytics with export capabilities
- **AI-Powered Assistant**: Conversational AI for HR queries and document intelligence

## AI Features

The system includes AI-powered capabilities to enhance user experience and automate HR operations:

### Conversational AI Assistant
| Feature | Complexity | Implementation Notes |
|---------|-----------|---------------------|
| Policy Queries | Easy | HR policy chatbot with static content and pre-defined Q&A using template matching |
| Leave Balance Check | Easy | Natural language queries for checking leave balance via direct HRIS API integration |
| Leave/PTO Requests | Medium | Conversational leave request submission with approval workflow integration |
| Multi-language Support | Easy | Support for multiple languages using translation APIs (Google Translate, DeepL) |
| 24/7 Availability | Medium | Always-on AI assistant with infrastructure monitoring and maintenance |

**Reference**: https://www.lindy.ai/

## Problem Statement

Organizations face challenges in human resource management including:
- **Manual Attendance Tracking**: Time-consuming paper-based or basic digital attendance systems
- **Payroll Complexity**: Manual payroll calculations leading to errors and compliance issues
- **Approval Bottlenecks**: Inefficient leave and overtime approval processes
- **Data Fragmentation**: Employee information scattered across multiple systems
- **Limited Self-Service**: Employees dependent on HR for basic requests and information access
- **Reporting Gaps**: Lack of comprehensive HR analytics and insights

## Solution Overview

A comprehensive HRIS platform that automates and streamlines HR processes through:
- **Intelligent Attendance**: GPS-based attendance with offline synchronization
- **Automated Payroll**: End-to-end payroll processing with tax compliance and bulk slip generation
- **Digital Workflows**: Configurable approval workflows for leave, overtime, and reimbursements
- **Employee Self-Service**: Complete self-service portal for profile management and requests
- **HR Analytics**: Comprehensive HR dashboards with reporting capabilities
- **Mobile-First Design**: Native mobile applications for field employees and managers

## User Stories

### Admin/HR Role
- **Employee Management**: Full CRUD operations for employee data, department, job positions, and organizational structure
- **System Configuration**: Configure approval workflows, shift schedules, payroll parameters, and system settings
- **Payroll Processing**: Execute payroll runs, generate bulk salary slips, manage tax calculations and BPJS integration
- **Reporting & Analytics**: Access comprehensive HR reports, export data in multiple formats, and monitor system usage
- **Attendance Monitoring**: Oversee attendance patterns, manage exceptions, and configure attendance rules

### Manager/Supervisor Role
- **Team Management**: View and manage direct reports, monitor team attendance
- **Approval Workflows**: Approve leave requests, overtime applications, and reimbursement claims for team members
- **Team Analytics**: Access team-specific reports and attendance summaries
- **Schedule Management**: Configure team shift schedules and manage work arrangements
- **Team Monitoring**: Receive notifications for pending approvals and team attendance alerts

### Employee Role
- **Self-Service Portal**: Update personal information, emergency contacts, and banking details
- **Attendance Tracking**: Clock in/out using mobile app with GPS verification
- **Leave Management**: Submit leave requests, track approval status, and view leave balance
- **Payroll Access**: Download salary slips, view payment history, and access tax documents
- **Notifications**: Receive push notifications for approval updates, payroll notifications, and important announcements

## System Modules

The HRIS system is organized into nine core modules with clear separation of concerns:

1. **Authentication Module**
   - User authentication and session management
   - Role-based access control (RBAC)

2. **Data Master Module**
   - Branch and office management
   - Employee information management
   - Department and job position hierarchy
   - Role and permission management

3. **Attendance & Time Management Module**
   - GPS-based attendance tracking
   - Shift scheduling with rolling shifts support
   - Offline attendance with synchronization
   - Attendance monitoring and reporting

4. **Employee Self Service (ESS) Module**
   - Personal profile management
   - Document upload and management
   - Leave balance and history tracking
   - Payroll information access

5. **Payroll & Compensation Module**
   - Salary calculation engine
   - Tax computation (PPh 21) and BPJS integration
   - Bulk payroll processing
   - Salary slip generation and distribution

6. **Leave & Overtime Management Module**
   - Leave request workflows
   - Overtime calculation and approval
   - Leave balance tracking
   - Holiday calendar management

7. **Approval Workflow Module**
   - Configurable approval matrix
   - Multi-level approval chains
   - Notification system for approvals
   - Approval audit trails

8. **Reporting Module**
   - HR dashboard with key metrics
   - Attendance and payroll reports
   - Export capabilities (PDF, Excel, CSV)

9. **Notification Module**
   - Push notifications for mobile app

## Business Flows

### Attendance Check-in Flow

```mermaid
sequenceDiagram
    participant M as Mobile App
    participant A as API Gateway
    participant AS as Attendance Service
    participant G as GPS Service
    participant D as Database
    participant N as Notification

    M->>A: Request check-in
    A->>AS: Validate attendance request
    AS->>G: Validate GPS location
    G-->>AS: Location verification
    AS->>D: Store attendance record
    D-->>AS: Confirmation
    AS->>N: Trigger notifications
    N->>M: Push attendance confirmation
    AS-->>A: Attendance success
    A-->>M: Check-in completed
```

### Leave Request Approval Flow

```mermaid
sequenceDiagram
    participant E as Employee (Mobile)
    participant A as API Gateway
    participant L as Leave Service
    participant AP as Approval Service
    participant M as Manager
    participant N as Notification
    participant D as Database

    E->>A: Submit leave request
    A->>L: Process leave request
    L->>D: Check leave balance
    D-->>L: Balance available
    L->>AP: Initiate approval workflow
    AP->>D: Create approval record
    AP->>N: Notify manager
    N->>M: Push notification
    M->>A: Review and approve
    A->>AP: Process approval
    AP->>L: Update leave status
    L->>D: Update leave record
    AP->>N: Notify employee
    N->>E: Approval notification
```


## Feature Specifications

### Employee Service
- Employee CRUD operations with validation
- Department and job position management
- Employee onboarding and offboarding workflows
- Profile picture and document management
- Employee search and filtering capabilities

### Attendance Service
- GPS-based location validation
- Offline attendance synchronization
- Shift management and scheduling
- Attendance analytics and reporting
- Exception handling and approvals

### Payroll Service
- Salary calculation engine with configurable rules
- Tax computation (PPh 21) and BPJS integration
- Overtime and allowance calculations
- Bulk payroll processing with job queues
- Payslip generation and distribution
- Payroll audit trails and reporting

### Leave Service
- Leave balance calculation and tracking
- Leave request validation and processing
- Holiday calendar management
- Leave type configuration
- Integration with approval workflows
- Leave analytics and reporting

### Approval Service
- Configurable approval matrix
- Multi-level approval chains
- Approval delegation and substitution
- Audit trails for all approvals
- Integration with notification system
- Approval analytics and monitoring

## Attendance System

### Attendance Methods

#### Mobile Attendance
- GPS tracking for location-based verification
- Offline attendance with automatic synchronization
- Push notifications for attendance confirmation

#### Web-Based Attendance
- Browser geolocation API for location detection
- Requires active internet connection
- Web notifications for attendance confirmation

## Security Features

### Authentication & Authorization
- **Primary Authentication**: Email/username and password with bcrypt hashing
- **Session Management**: JWT tokens with refresh token rotation
- **Role-Based Access Control**: Super Admin, HR Admin, Manager, and Employee roles

### Data Security
- **Data at Rest**: AES-256 encryption
- **Data in Transit**: HTTPS/TLS 1.3 for all API communications
- **File Storage**: Server-side encryption for uploaded documents and images

### Privacy Compliance
- **GDPR Compliance**: Right to erasure, data portability, and consent management
- **Data Minimization**: Collect only necessary employee information
- **Audit Logging**: Comprehensive audit trails for all data access and modifications
- **Data Retention**: Configurable data retention policies with automatic purging
- **Anonymization**: Employee data anonymization for reporting and analytics

### Application Security
- **Input Validation**: Comprehensive validation and sanitization of all inputs
- **SQL Injection Prevention**: Parameterized queries and ORM-level protection
- **XSS Protection**: Content Security Policy (CSP) and output encoding
- **CSRF Protection**: Double-submit cookie pattern and SameSite attributes
- **Rate Limiting**: API rate limiting to prevent abuse and DDoS attacks

## MVP Scope - Excluded Features

The following features are planned for future releases and NOT included in the MVP:

### Shift Management (Planned for Phase 2)
- Setting schedule shift
- Request shift change workflows
- Approval shift change with approval matrix
- Reporting shift changes - export to Excel
- Shift analytics and monitoring

### Advanced Approval Matrix Configuration
- Detailed approval matrix for attendance requests
- Detailed approval matrix for leaves
- Detailed approval matrix for overtime
- Detailed approval matrix for reimbursement/expense
- Approval delegation and escalation rules

### Mobile-Specific Enhancements
- Change password with email confirmation
- Clock in/out reminder notifications
- Offline approval schema (for Leaves, Overtime, Reimbursement, Attendance requests)
- Offline request submission with auto-sync

### Email & Communication Features
- Bulk email blast for salary slips
- Email notifications for approvals
- Email confirmation for password changes
- Email notifications for attendance alerts

### Advanced Reporting & Analytics
- Detailed HR dashboard (headcount breakdown, turnover analytics)
- Advanced attendance overview with trends
- Custom report builder
- Scheduled report delivery
- Real-time analytics dashboards

### Additional Features
- Approval history and audit trails per module
- Advanced document management system
- Employee performance tracking
- Training and development module
- Asset management integration

### Face Recognition Attendance (Deferred - Budget Constraint)
- Face recognition for attendance verification
- Multiple reference photos per employee
- Face matching and similarity scoring
- Integration with AWS Rekognition or similar services
- Camera-based attendance for mobile, web, and kiosk

## Glossary

- **HRIS**: Human Resource Information System - Comprehensive software for managing employee information and HR processes
- **ESS**: Employee Self Service - Portal allowing employees to manage their own information and requests
- **RBAC**: Role-Based Access Control - Security model that restricts access based on user roles
- **JWT**: JSON Web Token - Standard for securely transmitting information between parties
- **PPh 21**: Indonesian income tax regulation for employee salaries
- **BPJS**: Indonesian social security system for healthcare and employment
- **PWA**: Progressive Web App - Web applications with native app-like features
- **FCM**: Firebase Cloud Messaging - Cross-platform messaging solution for push notifications

