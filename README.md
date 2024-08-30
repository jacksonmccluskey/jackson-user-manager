# jackson-user-manager

This is a comprehensive user and organization management system built in full-stack TypeScript with Node.js, Express, and MongoDB.

It supports user authentication, role-based access control, organization management, and robust admin functionality.

The system is designed to be flexible and scalable, suitable for various industries, including healthcare, where organizations need to manage users and their data securely.

## Table of Contents

- [Features](#features)
- [Technologies Used (All TypeScript Plz)](#technologies-used-all-typescript-plz)
- [System Architecture](#system-architecture)
- [Installation](#installation)

## Features

- **User Authentication & Authorization**: User registration, login, password reset, and role-based access control (RBAC).
- **Organization Management**: Create and manage organizations, invite users, and manage branding.
- **Admin User Management Portal**: Manage users within an organization, including roles, activities, and bulk actions.
- **Logging & Monitoring**: Integrated with `jackson-load-balancer` for centralized logging and monitoring.

## Technologies Used (All TypeScript Plz)

- **Backend**: Node.js (with `express`)
- **Database**: MongoDB on Atlas (with `mongoose`)
- **Frontend**: Next.js (React.js Framework) (with `chakra-ui`)
- **Authentication**: JWT (JSON Web Tokens) (with `passport`)
- **Validation**: `joi`
- **Environment Management**: Docker (with `docker-compose`)
- **Logging**: `jackson-load-balancer`

## System Architecture

The system architecture is designed to separate concerns and ensure scalability:

1. **Frontend (Next.js + Chakra UI)**: Handles UI, including authentication pages, dashboards, and admin management portals.
2. **Backend (Node.js + Express + TypeScript)**: Exposes RESTful API endpoints, handles authentication, organization management, and user verification workflows.
3. **Database (MongoDB)**: Stores user, organization, and activity logs.
6. **Logging and Monitoring**: Centralized logging and monitoring with `jackson-load-balancer`.

## Installation

### Prerequisites

- Git
- Node.js (v20.x or higher)
- Docker and Docker Compose
- MongoDB Atlas

### 3 Steps

1. Clone & Install:

```
git clone https://github.com/jacksonmccluskey/jackson-load-balancer.git
cd jackson-load-balancer
npm install
cd ..
git clone https://github.com/jacksonmccluskey/jackson-user-manager.git
cd jackson-user-manager
npm install
```

2. Create a .env file in the root directory and configure the following environment variables:

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

3. Run Docker Compose

```
cd jackson-load-balancer
docker compose up -d
cd ..
cd jackson-user-manager
docker compose up -d
```
