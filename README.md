# jackson-user-manager

This is a comprehensive user and organization management system built in full-stack TypeScript with Node.js, Express, and MongoDB.

It supports user authentication, role-based access control, organization management, document storage, user intake forms, and robust admin functionality.

The system is designed to be flexible and scalable, suitable for various industries, including healthcare, where organizations need to manage users and their data securely.

## Table of Contents

- [Features](#features)
- [Technologies Used](#technologies-used)
- [System Architecture](#system-architecture)
- [Installation](#installation)
- [Environment Variables](#environment-variables)
- [API Endpoints](#api-endpoints)
- [Database Schemas](#database-schemas)
- [Running the Application](#running-the-application)
- [Deployment](#deployment)
- [License](#license)

## Features

- **User Authentication & Authorization**: User registration, login, password reset, and role-based access control (RBAC).
- **Organization Management**: Create and manage organizations, invite users, and manage branding.
- **User Intake & Verification**: Intake forms for user access requests and admin verification workflows.
- **Document Management**: Upload, retrieve, and delete documents with AWS S3 integration.
- **Admin User Management Portal**: Manage users within an organization, including roles, activities, and bulk actions.
- **Logging & Monitoring**: Integrated with `jackson-load-balancer` for centralized logging and monitoring.

## Technologies Used

- **Backend**: Node.js, Express.js, TypeScript
- **Database**: MongoDB with Mongoose
- **Frontend**: Next.js (React.js) with Chakra UI, TypeScript
- **Authentication**: JWT (JSON Web Tokens)
- **File Storage**: AWS S3
- **Interaction Storage**: Azure Container
- **Validation**: Joi
- **Environment Management**: Docker Compose
- **Logging**: Custom logging via `jackson-load-balancer`

## System Architecture

The system architecture is designed to separate concerns and ensure scalability:

1. **Frontend (Next.js + Chakra UI)**: Handles UI, including authentication pages, dashboards, and admin management portals.
2. **Backend (Node.js + Express + TypeScript)**: Exposes RESTful API endpoints, handles authentication, organization management, and user verification workflows.
3. **Database (MongoDB)**: Stores user, organization, document metadata, and activity logs.
4. **File Storage (AWS S3)**: Manages document storage with secure, scalable file uploads.
5. **Interaction Storage (Azure Container)**: Manages inputs and outputs (requests and responses) for user interaction data for future analysis.
6. **Logging and Monitoring**: Centralized logging and monitoring with `jackson-load-balancer`.

## Installation

### Prerequisites

- Node.js (v20.x or higher)
- Docker and Docker Compose
- MongoDB Atlas
- AWS (for S3 and ECR)

### Steps

1. Clone the repository:

   ```bash
   git clone https://github.com/jacksonmccluskey/jackson-user-manager.git
   cd jackson-user-manager
   ```

### Install dependencies:

```
npm install
```

2. Set up environment variables (see Environment Variables).

3. Build Docker images (optional for local development):

```
docker-compose build
```

### Environment Variables

Create a .env file in the root directory and configure the following environment variables:

```
# General Settings

PORT=22000
JWT_SECRET=JWT_SECRET
NODE_ENV=DEVELOPMENT
```

```
# MongoDB Settings

MONGO_URI=mongodb://localhost:4444/organizations
# Atlas: mongodb+srv://user:pass@urlstring.mongodb.net/organizations
```

# AWS settings

AWS_ACCESS_KEY_ID=AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY=AWS_SECRET_ACCESS_KEY
S3_BUCKET_NAME=S3_BUCKET_NAME

# Logging settings

JACKSON_LOAD_BALANCER_URL=http://jackson-load-balancer:5555
API Endpoints
Auth Routes
POST /api/auth/signup
Registers a new user or organization.
Body Parameters:
name: String
email: String
password: String
organization: String (optional for user signup)
POST /api/auth/login
Authenticates a user.
Body Parameters:
email: String
password: String
POST /api/auth/reset-password
Initiates password reset.
Body Parameters:
email: String
POST /api/auth/logout
Logs out a user by invalidating the JWT.
Organization Routes
POST /api/organizations
Creates a new organization.
Body Parameters:
name: String
adminEmail: String (used to create the first admin user)
branding: Object (e.g., logo, colors)
GET /api/organizations/
Retrieves organization details.
Path Parameters:
orgId: String
PUT /api/organizations/
Updates organization details.
Path Parameters:
orgId: String
Body Parameters:
name: String
branding: Object
User Management Routes
GET /api/users/

Retrieves user details.
Path Parameters:
userId: String
PUT /api/users/

Updates user profile information.
Path Parameters:
userId: String
Body Parameters:
name: String
email: String
GET /api/organizations/
/users

Retrieves all users within an organization.
Path Parameters:
orgId: String
PUT /api/organizations/
/users/
/verify

Verifies a user within an organization.
Path Parameters:
orgId: String
userId: String
Document Routes
POST /api/documents
Uploads a document for an organization.
Body Parameters:
orgId: String
document: File
GET /api/documents/
Lists all documents for an organization.
Path Parameters:
orgId: String
DELETE /api/documents/
Deletes a document.
Path Parameters:
docId: String
Invitation and Intake Routes
POST /api/invitations
Sends an invitation to a user (e.g., patient) to join an organization.
Body Parameters:
email: String
name: String
orgId: String
POST /api/intake
Submits an intake form for a user requesting access to an organization.
Body Parameters:
email: String
name: String
intakeData: Object
Database Schemas
User Schema
typescript
Copy code
import { Schema, model, Document } from 'mongoose';

interface IUser extends Document {
name: string;
email: string;
password: string;
organization?: Schema.Types.ObjectId;
role: 'admin' | 'member';
hasBeenVerified: boolean;
createdAt: Date;
updatedAt: Date;
}

const userSchema = new Schema<IUser>({
name: { type: String, required: true },
email: { type: String, required: true, unique: true },
password: { type: String, required: true },
organization: { type: Schema.Types.ObjectId, ref: 'Organization' },
role: { type: String, enum: ['admin', 'member'], default: 'member' },
hasBeenVerified: { type: Boolean, default: false },
createdAt: { type: Date, default: Date.now },
updatedAt: { type: Date, default: Date.now }
});

export const User = model<IUser>('User', userSchema);
Organization Schema
typescript
Copy code
import { Schema, model, Document } from 'mongoose';

interface IOrganization extends Document {
name: string;
branding: {
logo?: string;
colors?: Map<string, string>;
};
documents: Schema.Types.ObjectId[];
admins: Schema.Types.ObjectId[];
createdAt: Date;
updatedAt: Date;
}

const organizationSchema = new Schema<IOrganization>({
name: { type: String, required: true },
branding: {
logo: { type: String },
colors: { type: Map, of: String }
},
documents: [{ type: Schema.Types.ObjectId, ref: 'Document' }],
admins: [{ type: Schema.Types.ObjectId, ref: 'User' }],
createdAt: { type: Date, default: Date.now },
updatedAt: { type: Date, default: Date.now }
});

export const Organization = model<IOrganization>('Organization', organizationSchema);
Document Schema
typescript
Copy code
import { Schema, model, Document } from 'mongoose';

interface IDocument extends Document {
orgId: Schema.Types.ObjectId;
name: string;
path: string;
uploadedAt: Date;
}

const documentSchema = new Schema<IDocument>({
orgId: { type: Schema.Types.ObjectId, ref: 'Organization', required: true },
name: { type: String, required: true },
path: { type: String, required: true },
uploadedAt: { type: Date, default: Date.now }
});

export const Document = model<IDocument>('Document', documentSchema);
Invitation Schema
typescript
Copy code
import { Schema, model, Document } from 'mongoose';

interface IInvitation extends Document {
orgId: Schema.Types.ObjectId;
email: string;
name: string;
inviteCode: string;
sentAt: Date;
acceptedAt?: Date;
}

const invitationSchema = new Schema<IInvitation>({
orgId: { type: Schema.Types.ObjectId, ref: 'Organization', required: true },
email: { type: String, required: true },
name: { type: String, required: true },
inviteCode: { type: String, required: true },
sentAt: { type: Date, default: Date.now },
acceptedAt: { type: Date }
});

export const Invitation = model<IInvitation>('Invitation', invitationSchema);
Running the Application
Local Development
Start the MongoDB server:

bash
Copy code
mongod --config /usr/local/etc/mongod.conf
Run the backend server:

bash
Copy code
npm run dev
Start the frontend (if using a separate frontend service):

bash
Copy code
cd frontend
npm run dev
Docker Development
Start all services using Docker Compose:

bash
Copy code
docker-compose up --build
Access the application at http://localhost:4000.

Deployment
CI/CD Pipeline
Build and Test: Use GitHub Actions to automate testing and Docker image building.
Push to AWS ECR: Push the Docker images to your Amazon Elastic Container Registry.
Deploy to AWS EC2: Deploy the containers to your EC2 instances using SSH and Docker Compose.
