# HRIS Future Planning

## MVP Scope - Excluded Features

The following features are planned for future releases and NOT included in the MVP:

### AI-Powered Features (Deferred)

#### Document Intelligence
| Feature | Complexity | Implementation Notes |
|---------|-----------|---------------------|
| Real-time Document Updates | Medium | Automatic document indexing with embedding pipeline and change detection |
| Integration with Document Systems | Medium | Connect with SharePoint, Google Drive, and Confluence for unified document access |

#### Expense Management with AI
| Feature | Complexity | Implementation Notes |
|---------|-----------|---------------------|
| Receipt OCR & Auto-fill | Medium | Automatic receipt scanning and data extraction using OCR (Tesseract/AWS Textract) |
| Expense Categorization | Easy | AI-based automatic expense category suggestion |
| Receipt Upload & Management | Medium | Submit reimbursement requests with receipt uploads and track approval status |
| Duplicate Detection | Medium | AI detection of duplicate expense submissions |
| Policy Compliance Check | Hard | Automated expense policy validation using rule engine |

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
- Employee performance tracking and KPI metrics
- Training and development module
- Asset management integration

### Face Recognition Attendance (Deferred - Budget Constraint)
- Face recognition for attendance verification
- Multiple reference photos per employee
- Face matching and similarity scoring
- Integration with AWS Rekognition or similar services
- Camera-based attendance for mobile, web, and kiosk
