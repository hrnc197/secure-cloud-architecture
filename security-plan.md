# Secure Cloud Architecture Plan

## Secure Cloud Architecture Flow
Users ↓ CDN ↓ Load Balancer ↓ Application Servers ↓ Private Database

## Component Explanations
- **CDN:** The CDN stores cached copies of static content closer to users to improve loading speed[cite: 2].
- **Load Balancer:** The load balancer distributes incoming requests across multiple application servers[cite: 2].
- **Application Servers:** Application servers process requests from users. These servers should be placed in a private subnet[cite: 2].
- **Database:** The database stores student records. The database should remain private and should not be directly accessible from the Internet[cite: 2].

# Public and Private Resources

| Resource | Public or Private? | Explanation |
| :--- | :--- | :--- |
| **CDN** | Public | Positioned at the network edge globally to serve static assets directly and quickly to users over the internet. |
| **Load Balancer** | Public | Serves as the single public entry point facing the internet, accepting incoming client traffic and routing it safely backend-side. |
| **Application Server** | Private | Hidden behind the load balancer in a private subnet, handling core business logic away from direct internet exposure. |
| **Database** | Private | Isolated entirely in a protected private subnet with no direct public route, ensuring max data security. |

# Security Controls
- **IAM:** Access to the cloud environment should be strictly restricted using Identity and Access Management, enforcing least-privilege role allocations.
- **MFA:** Multi-Factor Authentication must be mandated for all administrator, privileged, and production deployment accounts.
- **Firewall / Security Group:** Network access must follow strict perimeter guardrails.
  - Internet → Load Balancer = Allowed[cite: 2]
  - Load Balancer → Application Server = Allowed[cite: 2]
  - Application Server → Database = Allowed[cite: 2]
  - Internet → Database = Blocked[cite: 2]
- **Encryption:** Student information must be encrypted both in transit (using HTTPS/TLS) and at rest (using database storage encryption) to prevent eavesdropping and data leaks.
- **Logging:** All administrative logins, privilege elevations, configuration shifts, and data access anomalies should be recorded continuously.
- **Monitoring:** Automated alerts and metrics should track unusual traffic spikes, unauthorized API calls, and resource throttling anomalies.
- **Backup:** The database should have regular automated daily backups and point-in-time recovery capabilities to safeguard against data corruption.

# Principle of Least Privilege

| User | Allowed Access |
| :--- | :--- |
| **Administrator** | Full infrastructure configuration and user access management control plane. |
| **Instructor** | Access to view student records, manage course entries, and oversee grading logs. |
| **Student** | Access strictly limited to viewing personal profile details and self-enrollment summaries. |
| **Developer** | Access restricted to staging deployment repositories, application logs, and code testing environments. |

# Shared Responsibility Model

| Responsibility | Cloud Provider or Customer? |
| :--- | :--- |
| Physical data center | Cloud Provider |
| Physical servers | Cloud Provider |
| User accounts | Customer |
| Student data | Customer |
| IAM permissions | Customer |
| Application security | Customer |
| Database access rules | Customer |
| Backups | Customer |

- **What does Security OF the Cloud mean?** It refers to the protection of the underlying global infrastructure that runs all cloud services—including hardware, software, physical data centers, networking facilities, and regional availability zones.
- **What does Security IN the Cloud mean?** It refers to the customer's responsibility regarding configuration, management, and operational security of guest operating systems, application layers, database contents, network firewalls, and IAM credential controls.

# Architecture Questions

### 1. Which resource should be directly accessible from the Internet?
Only the Load Balancer and the CDN edge nodes should accept direct incoming requests from the public internet.

### 2. Why should the database private?
To protect sensitive student information from direct malicious exposure, credential sniffing, and unauthorized external data injection attacks.

### 3. Why should users not connect directly to the database?
Direct connections bypass business logic, security filters, and access controls, creating critical database vulnerability vectors.

### 4. What is the purpose of a load balancer?
To evenly distribute incoming client request traffic across multiple application servers to prevent overloading and ensure high availability.

### 5. What happens if one application server fails?
The load balancer automatically routes incoming traffic away from the failed server to remaining healthy instances, maintaining uninterrupted service.

### 6. What is the purpose of a CDN?
To cache static files locally closer to edge users, minimizing latency and reducing processing loads on core server infrastructure.

### 7. Why should administrator accounts use MFA?
Because compromised admin credentials grant total system control; MFA adds a secondary verification layer even if passwords are leaked.

### 8. Why should administrator access not be given to every employee?
To limit internal threat surfaces, minimize accidental configuration destruction, and comply with the principle of least privilege.

### 9. Why are logging and monitoring important?
They provide complete infrastructure visibility, enabling rapid incident response, forensic auditing, and anomaly detection.

### 10. Why are backups important?
They ensure data continuity and fast system recovery in the event of accidental deletions, hardware failures, or ransomware incidents.