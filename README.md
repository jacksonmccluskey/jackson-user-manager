# jackson-user-manager (temporary @mryechkin fork w/ supabase)

This is a comprehensive user and organization management system built in full-stack TypeScript with Node.js, Express, and MongoDB.

It supports user authentication, role-based access control, organization management, and robust admin functionality.

The system is designed to be flexible and scalable, suitable for various industries, including healthcare, where organizations need to manage users and their data securely.

# Table of Contents

- [Features](#features)
- [Technologies Used (All TypeScript)](#technologies-used-all-typescript)
- [System Architecture](#system-architecture)
- [Prerequisites](#prerequisites)
- [3 Steps](#3-steps)
- [Routes](#routes)

# Features

- **User Authentication & Authorization**: User registration, login, password reset, and role-based access control (RBAC).
- **Organization Management**: Create and manage organizations, invite users, and manage branding.
- **Admin User Management Portal**: Manage users within an organization, including roles, activities, and bulk actions.
- **Logging & Monitoring**: Integrated with `jackson-load-balancer` for centralized logging and monitoring.

# Technologies Used (All TypeScript)

- **Backend**: Node.js (with `express`)
- **Database**: MongoDB on Atlas (with `mongoose`)
- **Frontend**: Next.js (React.js Framework) (with `chakra-ui`)
- **Authentication**: JWT (JSON Web Tokens) (with `passport`)
- **Validation**: `joi`
- **Environment Management**: Docker (with `docker-compose`)
- **Logging**: `jackson-load-balancer`

# System Architecture

The system architecture is designed to separate concerns and ensure scalability:

1. **Frontend (Next.js + Chakra UI)**: Handles UI, including authentication pages, dashboards, and admin management portals.
2. **Backend (Node.js + Express + TypeScript)**: Exposes RESTful API endpoints, handles authentication, organization management, and user verification workflows.
3. **Database (MongoDB)**: Stores user, organization, and activity logs.
4. **Logging and Monitoring**: Centralized logging and monitoring with `jackson-load-balancer`.

# Prerequisites

- Git
- Node.js (v20.x or higher)
- Docker and Docker Compose
- MongoDB Atlas

# 3 Steps

### 1. Clone & Install:

```
git clone https://github.com/jacksonmccluskey/jackson-load-balancer.git
cd jackson-load-balancer
npm install
cd ..
git clone https://github.com/jacksonmccluskey/jackson-user-manager.git
cd jackson-user-manager
npm install
```

### 2. Create a .env file in the root directory and configure the following environment variables:

```
# General Settings

PORT=22222
NODE_ENV=DEVELOPMENT

# MongoDB Settings

MONGO_URI=mongodb://localhost:4444/jackson-database
# Atlas: mongodb+srv://user:pass@urlstring.mongodb.net/jackson-database

# Logging settings

JACKSON_LOAD_BALANCER_URL=http://jackson-load-balancer:5555
JACKSON_LOAD_BALANCER_LOG_ROUTE=/jackson/log
JACKSON_LOAD_BALANCER_EMAIL_ROUTE=/jackson/email
```

### 3. Run Docker Compose

```
cd jackson-load-balancer
docker compose up -d
cd ..
cd jackson-user-manager
docker compose up -d
```

# Routes

### User Data Management

User Registration & Profile:

- POST /api/users/register - Register a new user with initial role and organization assignment.
- GET /api/users/profile - Get the current user's profile, including detailed fields like profile, settings, securityQuestions, etc.
- PUT /api/users/profile - Update the current user's profile, including profile, address, emergencyContacts, and preferences.
- PUT /api/users/profile/security - Update security settings, including securityQuestions and twoFactorAuthEnabled.
- PUT /api/users/profile/settings - Update user settings, such as theme and notification preferences.

Password Management:

- POST /api/users/password-reset - Request a password reset, with tracking of auditLogs for the action.
- POST /api/users/password-reset/confirm - Confirm and set a new password with auditLogs entry.
- PUT /api/users/password/change - Change the user's password when logged in, with auditLogs entry.

Session Management:

- GET /api/users/sessions - List active sessions for the current user, using auditLogs to track login events.
- DELETE /api/users/sessions/:sessionId - Log out from a specific session, with an entry in auditLogs.

### Authentication

User Authentication:

- POST /api/auth/login - Log in a user, updating lastLogin and adding a login event to auditLogs.
- POST /api/auth/logout - Log out a user from all sessions or a specific session, with auditLogs entry.
- POST /api/auth/refresh-token - Refresh JWT tokens, with a tokenRefresh event in auditLogs.
- POST /api/auth/mfa-setup - Set up multi-factor authentication (MFA) using twoFactorAuthEnabled and twoFactorAuthSecret.
- POST /api/auth/mfa-verify - Verify MFA during login, with auditLogs entry for successful or failed attempts.

### Administration

Admin User Management:

- GET /api/admin/users - Get all users, with filtering by roles, organizations, and isActive status.
- GET /api/admin/users/:userId - Get detailed information about a specific user by ID, including their roles, organizations, and audit logs.
- PUT /api/admin/users/:userId - Update a user’s details, including roles, organizations, and isActive status.
- DELETE /api/admin/users/:userId - Deactivate or delete a user, setting isActive to false and recording the action in auditLogs.
- PUT /api/admin/users/:userId/activate - Reactivate a deactivated user, updating isActive and logging the event.

### Role-Based Access Control (RBAC)

Role Management:

- POST /api/rbac/roles - Create a new role, which can be assigned to users and linked to organizations.
- GET /api/rbac/roles - Get all roles, with filtering based on usage within organizations.
- GET /api/rbac/roles/:roleId - Get details of a specific role, including which users and organizations it is associated with.
- PUT /api/rbac/roles/:roleId - Update a role’s details, including permissions, with changes recorded in auditLogs.
- DELETE /api/rbac/roles/:roleId - Delete a role, ensuring that users and organizations are updated accordingly.

Permission Management:

- POST /api/rbac/permissions - Create a new permission, to be assigned to roles.
- GET /api/rbac/permissions - Get all permissions, with usage statistics across roles and organizations.
- GET /api/rbac/permissions/:permissionId - Get details of a specific permission, including which roles it is associated with.
- PUT /api/rbac/permissions/:permissionId - Update permission details, with auditLogs tracking changes.
- DELETE /api/rbac/permissions/:permissionId - Delete a permission, with automatic updates to roles that included it.

Assigning Roles & Permissions:

- POST /api/rbac/roles/:roleId/assign - Assign a role to a user, linking it to the appropriate organization.
- DELETE /api/rbac/roles/:roleId/revoke - Revoke a role from a user, with updates to the user’s roles and corresponding audit logs.
- POST /api/rbac/roles/:roleId/permissions - Assign permissions to a role, with inheritance and audit logging.
- DELETE /api/rbac/roles/:roleId/permissions/:permissionId - Remove a permission from a role, with impact assessment and logging.

### User Invitations

User Invitation Management:

- POST /api/invitations/send - Send an invitation to a user, generating an invitation code linked to a user document.
- POST /api/invitations/resend/:invitationId - Resend an invitation, updating the invitation status in the user document.
- POST /api/invitations/accept - Accept an invitation, setting hasAccepted to true and linking the user to their roles and organizations.
- GET /api/invitations/status/:invitationId - Check the status of an invitation, including whether it has been accepted.

### Logging & Audit

Audit Log Management:

- GET /api/audit-logs - Get all audit logs with advanced filtering (by user, organization, action, date).
- GET /api/audit-logs/:auditLogId - Get details of a specific audit log, including associated users and organizations.
- GET /api/audit-logs/users/:userId - Get audit logs related to a specific user, tracking actions such as logins, role changes, and updates.

System Logging:

- GET /api/logs - Access system logs for monitoring and debugging, including those related to user and organization actions.
