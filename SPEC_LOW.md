### Server Specifications

#### Production/Development Environment (1000+ Users)
- **Docker Host Server**: 
  - CPU: 8 vCPUs
  - RAM: 32 GiB
  - Storage: 640 GiB NVMe SSD
  - Network: 10,000 GiB transfer included
  - Container Runtime: Docker with Docker Compose
  - Price: $192.00/month
  - Provider: Digital Ocean Basic Droplets (https://www.digitalocean.com/pricing/droplets#basic-droplets)
  - **Assumed Price**: $192/month

- **Storage**: S3 (Asia Pacific Jakarta)
  - **Storage Requirements Analysis for 1000 employees:**
    - Face images (3mo retention): 364GB (1000 × 2 photos/day × 90 days × 2MB)
    - Reimbursement documents: ~2GB/year
    - Payroll documents: ~1.5GB/year
    - HR documents: ~3GB
    - **Total estimated: ~370GB** (exceeds S3 budget significantly)
  - **S3 Storage**: For file storage (images, documents)
    - Standard storage: 370GB × $0.025/GB = ~$9.25/month
    - PUT requests: 180,000 × $0.0053/1000 = ~$0.95/month
    - GET requests: 1,800,000 × $0.0042/10,000 = ~$0.76/month
    - **S3 Total**: ~$10.96/month
  - **Assumed Price**: $11/month

- **Notifications**: Firebase Cloud Messaging (FCM)
  - Price: Free (Spark plan sufficient)
  - Unlimited notifications
  - Cross-platform support (web, mobile)
  - No upgrade to paid plan needed for notifications only
  - **Assumed Price**: $0/month

- **Face Recognition**: AWS Rekognition (Asia Pacific Singapore)
  - Face Comparison: $0.0013 per CompareFaces call (first 1M images tier - worst case)
  - No indexing or storage costs (direct photo comparison)
  - Base monthly cost: $20-78 for 1000 employees (includes clock-in/out)
  - Additional overhead: Failed attempts (+20%), Poor photo quality (+10%)
  - **Total estimated cost: $26-101 for 1000 employees**
  - Calculation breakdown:
    - Base: 1000 employees × 2 calls/day × 30 days = 60,000 calls = $78/month
    - With retries/re-captures: 60,000 × 1.30 = 78,000 calls = $101.40/month
    - With 75% attendance: 58,500 calls = $76/month  
    - With 50% attendance: 39,000 calls = $50.70/month
    - With 25% attendance: 19,500 calls = $25.35/month
  - Reference: https://aws.amazon.com/rekognition/pricing/
  - **Assumed Price**: $100/month

- **Monitoring**: Sentry Team Plan
  - Price: $26/month
  - Error tracking and performance monitoring
  - Up to 5 team members included
  - Reference: https://sentry.io/pricing/
  - **Assumed Price**: $26/month

### **Total Infrastructure Cost Summary**

**Monthly Total:**
- Docker Host Server (includes self-hosted PostgreSQL): $192/month
- Storage (AWS S3): $11/month
- Firebase FCM: $0/month
- AWS Rekognition: $100/month (worst case)
- Sentry Monitoring: $26/month
- **Total per month: $329/month**

**Cost Savings:**
- Eliminated managed database cost: -$110.50/month
- **Annual savings: $1,326/year**

**Yearly Total:**
- **Total per year: $3,948/year**



### **Docker Host Server Resource Allocation (32GB RAM, 8 vCPUs)**

**Services Running:**
- NGINX Load Balancer
- Redis Cache
- Go Backend (9 HRIS modules)
- Next.js Frontend
- PostgreSQL Database

#### **RAM Allocation (32GB Total):**

- **NGINX Load Balancer: 512MB**
  - Handles 1000+ concurrent connections
  - Static file serving and SSL termination

- **Redis Cache: 4GB** 
  - Session storage (1000+ users)
  - Employee data caching
  - Attendance data temporary storage
  - Face recognition cache

- **Go Backend Services: 8GB**
  - Employee Service: 1.5GB (CRUD, search, validation)
  - Attendance Service: 2GB (GPS validation, face recognition processing)
  - Payroll Service: 2GB (complex calculations, tax computation)
  - Leave/Approval Service: 1GB (workflow processing)
  - Reporting Service: 1GB (data aggregation, export generation)
  - API Gateway/Router: 0.5GB

- **Next.js Frontend: 4GB**
  - Server-side rendering for 1000+ users
  - Static asset serving and API request handling

- **PostgreSQL Database: 12GB**
  - Shared buffers: 3GB (25% of allocated RAM)
  - Work memory: 2GB (for complex queries)
  - Connection overhead: 1GB (1000+ users)
  - Buffer cache: 6GB (query performance)

- **System Overhead: 3.5GB**
  - Docker containers overhead
  - OS processes and network buffers
  - File system cache

#### **CPU Allocation (8 vCPUs Total):**

- **NGINX**: 0.5 cores
- **Redis**: 0.5 cores  
- **Go Backend**: 3.5 cores (distributed across all services)
- **Next.js**: 1.5 cores (SSR processing)
- **PostgreSQL**: 2 cores (query processing, indexing)

**Risk Assessment:**
- Medium Risk: Tight on PostgreSQL memory for complex payroll queries
- Monitor PostgreSQL performance during peak usage and payroll processing
- Face Recognition uses AWS API, reducing local processing load
