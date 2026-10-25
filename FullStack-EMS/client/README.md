<p align="center">
  <h1 align="center">⚡ QuickEMS</h1>
  <p align="center">
    A full-stack <strong>Employee Management System</strong> built with the MERN stack.
    <br />
    Manage employees, track attendance, handle leave requests, and generate payslips — all from one unified dashboard.
  </p>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=white" alt="React" />
  <img src="https://img.shields.io/badge/Express-5-000000?logo=express&logoColor=white" alt="Express" />
  <img src="https://img.shields.io/badge/MongoDB-Atlas-47A248?logo=mongodb&logoColor=white" alt="MongoDB" />
  <img src="https://img.shields.io/badge/Node.js-20+-339933?logo=nodedotjs&logoColor=white" alt="Node.js" />
  <img src="https://img.shields.io/badge/TailwindCSS-4-06B6D4?logo=tailwindcss&logoColor=white" alt="TailwindCSS" />
  <img src="https://img.shields.io/badge/Vite-8-646CFF?logo=vite&logoColor=white" alt="Vite" />
  <img src="https://img.shields.io/badge/Deployed_on-Vercel-000?logo=vercel&logoColor=white" alt="Vercel" />
</p>

---

## Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Architecture](#-architecture)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Environment Variables](#environment-variables)
  - [Seed the Admin Account](#seed-the-admin-account)
  - [Running Locally](#running-locally)
- [API Reference](#-api-reference)
- [Database Models](#-database-models)
- [Background Jobs (Inngest)](#-background-jobs-inngest)
- [Deployment](#-deployment)
- [Default Credentials](#-default-credentials)
- [License](#-license)

---

##  Overview

**QuickEMS** is a production-ready Employee Management System designed for small-to-medium organizations. It provides separate portals for **Admins** and **Employees**, each with role-specific dashboards and functionality.

Admins can onboard employees, monitor attendance, approve/reject leave requests, and generate monthly payslips. Employees can check in/out, apply for leave, view their payslips, and manage their profiles.

---

##  Features

###  Authentication & Authorization
- JWT-based authentication with role-based access control (`ADMIN` / `EMPLOYEE`)
- Separate login portals for admin and employees
- Password change functionality
- Protected routes with middleware guards

###  Employee Management *(Admin)*
- Add, edit, soft-delete, and restore employees
- Assign departments, positions, and salary details
- Track employment status (Active / Inactive)
- Search and filter across 10 departments

###  Attendance Tracking
- **Employee:** One-click check-in / check-out
- **Admin:** View attendance records for all employees by date
- Automatic working hours calculation with day-type classification:
  - Full Day (≥ 8 hrs) · Three Quarter Day (≥ 6 hrs) · Half Day (≥ 4 hrs) · Short Day (< 4 hrs)
- Auto check-out after 10 hours (via background job)

### Leave Management
- Employees can apply for **Sick**, **Casual**, or **Annual** leave
- Admins can approve or reject applications
- Leave status tracking (Pending → Approved / Rejected)
- 24-hour reminder email to admin for pending applications

###  Payslip Generation *(Admin)*
- Generate monthly payslips with salary breakdown (Basic + Allowances − Deductions)
- Employees can view and print their payslips
- Printable payslip page with clean layout

### Dashboard
- **Admin Dashboard:** Total employees, departments, recent attendance stats, leave requests summary
- **Employee Dashboard:** Personal profile, today's attendance, recent leaves, and payslips

###  Email Notifications
- Attendance check-out reminder (after 9 hours of check-in)
- Leave application reminder to admin (after 24 hours of pending)
- Daily attendance reminder to absent employees (11:30 AM IST cron)

###  Profile & Settings
- Employees can update their bio and personal info
- Change password from the settings page

---

## Tech Stack

| Layer          | Technology                                                                 |
|----------------|---------------------------------------------------------------------------|
| **Frontend**   | React 19, React Router 7, TailwindCSS 4, Vite 8, Lucide Icons           |
| **Backend**    | Node.js, Express 5, Mongoose 9                                           |
| **Database**   | MongoDB Atlas                                                             |
| **Auth**       | JSON Web Tokens (JWT), bcrypt                                             |
| **Email**      | Nodemailer (Brevo SMTP)                                                   |
| **Background** | Inngest (event-driven functions & cron jobs)                              |
| **File Upload**| Multer                                                                    |
| **HTTP Client**| Axios                                                                     |
| **Toasts**     | React Hot Toast                                                           |
| **Date Utils** | date-fns                                                                  |
| **Deployment** | Vercel (frontend + backend)                                               |

---

## Architecture

```
┌─────────────────────┐         ┌──────────────────────┐
│                     │  HTTP   │                      │
│   React Frontend    │◄───────►│   Express Backend    │
│   (Vite + TW4)      │  REST   │   (Node.js)          │
│                     │         │                      │
│   Port: 5173        │         │   Port: 4000         │
└─────────────────────┘         └──────────┬───────────┘
                                           │
                                ┌──────────▼───────────┐
                                │                      │
                                │   MongoDB Atlas      │
                                │   (Cloud Database)   │
                                │                      │
                                └──────────┬───────────┘
                                           │
                                ┌──────────▼───────────┐
                                │                      │
                                │   Inngest            │
                                │   (Background Jobs)  │
                                │                      │
                                └──────────┬───────────┘
                                           │
                                ┌──────────▼───────────┐
                                │                      │
                                │   Brevo SMTP         │
                                │   (Email Service)    │
                                │                      │
                                └──────────────────────┘
```

---

## Project Structure

```
FullStack-EMS/
├── client/                          # React Frontend
│   ├── public/                      # Static assets
│   ├── src/
│   │   ├── api/                     # Axios API client
│   │   ├── assets/                  # Images & icons
│   │   ├── components/
│   │   │   ├── attendance/          # Attendance components
│   │   │   ├── leave/               # Leave components
│   │   │   ├── payslip/             # Payslip components
│   │   │   ├── AdminDashboard.jsx   # Admin dashboard view
│   │   │   ├── EmployeeDashboard.jsx# Employee dashboard view
│   │   │   ├── EmployeeCard.jsx     # Employee list card
│   │   │   ├── EmployeeForm.jsx     # Add/Edit employee form
│   │   │   ├── LoginForm.jsx        # Login form component
│   │   │   ├── LoginLeftSide.jsx    # Login page branding
│   │   │   ├── ProfileForm.jsx      # Profile edit form
│   │   │   ├── Sidebar.jsx          # Navigation sidebar
│   │   │   ├── ChangePasswordModal.jsx
│   │   │   └── Loading.jsx          # Loading spinner
│   │   ├── context/
│   │   │   └── AuthContext.jsx      # Auth state management
│   │   ├── pages/
│   │   │   ├── Attendance.jsx
│   │   │   ├── Dashboard.jsx
│   │   │   ├── Employees.jsx
│   │   │   ├── Layout.jsx           # Authenticated layout shell
│   │   │   ├── Leave.jsx
│   │   │   ├── LoginLanding.jsx     # Role selection page
│   │   │   ├── Payslips.jsx
│   │   │   ├── PrintPayslip.jsx     # Printable payslip
│   │   │   └── Settings.jsx
│   │   ├── App.jsx                  # Root component & routes
│   │   ├── main.jsx                 # React entry point
│   │   └── index.css                # Global styles
│   ├── index.html
│   ├── vite.config.js
│   ├── vercel.json                  # Frontend deployment config
│   └── package.json
│
├── server/                          # Express Backend
│   ├── config/
│   │   ├── db.js                    # MongoDB connection
│   │   └── nodemailer.js            # SMTP email config
│   ├── constants/
│   │   └── departments.js           # Department enum list
│   ├── controllers/
│   │   ├── authController.js        # Login, register, password
│   │   ├── employeeController.js    # CRUD operations
│   │   ├── attendanceController.js  # Check-in/out logic
│   │   ├── leaveController.js       # Leave CRUD & approval
│   │   ├── payslipController.js     # Payslip generation
│   │   ├── profileController.js     # Profile updates
│   │   └── dashboardController.js   # Dashboard stats
│   ├── inngest/
│   │   └── index.js                 # Background jobs & cron
│   ├── middleware/
│   │   └── auth.js                  # JWT auth & admin guard
│   ├── models/
│   │   ├── User.js                  # User (email, password, role)
│   │   ├── Employee.js              # Employee profile & salary
│   │   ├── Attendance.js            # Daily attendance records
│   │   ├── LeaveApplication.js      # Leave requests
│   │   └── Payslip.js               # Monthly payslips
│   ├── routes/
│   │   ├── authRoutes.js
│   │   ├── employeeRoutes.js
│   │   ├── attendanceRoutes.js
│   │   ├── leaveRoutes.js
│   │   ├── payslipsRoutes.js
│   │   ├── profileRoutes.js
│   │   └── dashboardRoutes.js
│   ├── server.js                    # App entry point
│   ├── seed.js                      # Admin account seeder
│   ├── vercel.json                  # Backend deployment config
│   └── package.json
│
└── .gitignore
```

---

##  Getting Started

### Prerequisites

- **Node.js** ≥ 18
- **npm** ≥ 9
- **MongoDB Atlas** account (or local MongoDB instance)
- **Inngest** account (for background jobs — [inngest.com](https://www.inngest.com/))
- **Brevo** account (for SMTP email — [brevo.com](https://www.brevo.com/))

### Installation

```bash
# Clone the repository
git clone https://github.com/ygdarsill/Employee-management-system.git
cd Employee-management-system/FullStack-EMS

# Install server dependencies
cd server
npm install

# Install client dependencies
cd ../client
npm install
```

### Environment Variables

#### Server (`server/.env`)

Create a `.env` file in the `server/` directory:

```env
# JWT
JWT_SECRET="your-secret-jwt-key"

# Admin Email
ADMIN_EMAIL="admin@example.com"

# Database
MONGODB_URI="mongodb+srv://<user>:<password>@<cluster>.mongodb.net/<dbname>"

# Inngest (Background Jobs)
INNGEST_EVENT_KEY="your-inngest-event-key"
INNGEST_SIGNING_KEY="your-inngest-signing-key"

# SMTP (Brevo)
SMTP_USER="your-brevo-smtp-login"
SMTP_PASS="your-brevo-smtp-password"
SENDER_EMAIL="noreply@example.com"
```


### Seed the Admin Account

Before using the app, seed the initial admin user:

```bash
cd server
npm run seed
```

This creates an admin account with:
- **Email:** Value of `ADMIN_EMAIL` in your `.env`
- **Password:** `admin123`

>  **Change the default password immediately after first login.**

##  API Reference

All API routes are prefixed with `/api`. Authentication uses Bearer JWT tokens.

### Auth Routes — `/api/auth`

| Method | Endpoint         | Access    | Description               |
|--------|------------------|-----------|---------------------------|
| POST   | `/login`         | Public    | Login (returns JWT)        |
| POST   | `/register`      | Admin     | Register a new employee    |
| POST   | `/change-password`| Protected| Change current password    |

### Employee Routes — `/api/employees`

| Method | Endpoint       | Access | Description                   |
|--------|----------------|--------|-------------------------------|
| GET    | `/`            | Admin  | List all employees            |
| GET    | `/:id`         | Admin  | Get employee by ID            |
| PUT    | `/:id`         | Admin  | Update employee details       |
| DELETE | `/:id`         | Admin  | Soft-delete an employee       |
| PATCH  | `/restore/:id` | Admin  | Restore a deleted employee    |

### Attendance Routes — `/api/attendance`

| Method | Endpoint     | Access    | Description                   |
|--------|--------------|-----------|-------------------------------|
| POST   | `/check-in`  | Employee  | Check in for today            |
| POST   | `/check-out` | Employee  | Check out for today           |
| GET    | `/`          | Protected | Get attendance records        |

### Leave Routes — `/api/leave`

| Method | Endpoint       | Access    | Description                   |
|--------|----------------|-----------|-------------------------------|
| POST   | `/apply`       | Employee  | Submit leave application      |
| GET    | `/`            | Protected | Get leave applications        |
| PUT    | `/:id/status`  | Admin     | Approve or reject leave       |

### Payslip Routes — `/api/payslips`

| Method | Endpoint       | Access    | Description                   |
|--------|----------------|-----------|-------------------------------|
| POST   | `/generate`    | Admin     | Generate a payslip            |
| GET    | `/`            | Protected | Get payslips                  |
| GET    | `/:id`         | Protected | Get payslip by ID             |

### Profile Routes — `/api/profile`

| Method | Endpoint | Access    | Description                  |
|--------|----------|-----------|------------------------------|
| GET    | `/`      | Protected | Get current user's profile   |
| PUT    | `/`      | Protected | Update profile               |

### Dashboard Routes — `/api/dashboard`

| Method | Endpoint | Access    | Description                  |
|--------|----------|-----------|------------------------------|
| GET    | `/`      | Protected | Get dashboard statistics     |

---

##  Database Models

### User
| Field      | Type     | Description                        |
|------------|----------|------------------------------------|
| `email`    | String   | Unique email address               |
| `password` | String   | Bcrypt-hashed password             |
| `role`     | Enum     | `ADMIN` or `EMPLOYEE`              |

### Employee
| Field              | Type     | Description                        |
|--------------------|----------|------------------------------------|
| `userId`           | ObjectId | Reference to User                  |
| `firstName`        | String   | First name                         |
| `lastName`         | String   | Last name                          |
| `email`            | String   | Work email                         |
| `phone`            | String   | Phone number                       |
| `position`         | String   | Job title                          |
| `department`       | Enum     | One of 10 departments              |
| `basicSalary`      | Number   | Monthly base salary                |
| `allowances`       | Number   | Additional allowances              |
| `deductions`       | Number   | Deductions                         |
| `employmentStatus` | Enum     | `ACTIVE` or `INACTIVE`             |
| `joinDate`         | Date     | Date of joining                    |
| `isDeleted`        | Boolean  | Soft-delete flag                   |
| `bio`              | String   | Short biography                    |

### Attendance
| Field          | Type     | Description                           |
|----------------|----------|---------------------------------------|
| `employeeId`   | ObjectId | Reference to Employee                 |
| `date`         | Date     | Attendance date                       |
| `checkIn`      | Date     | Check-in timestamp                    |
| `checkOut`     | Date     | Check-out timestamp                   |
| `status`       | Enum     | `PRESENT`, `ABSENT`, or `LATE`        |
| `workingHours` | Number   | Calculated working hours              |
| `dayType`      | Enum     | Full Day / Three Quarter / Half / Short|

### LeaveApplication
| Field        | Type     | Description                           |
|--------------|----------|---------------------------------------|
| `employeeId` | ObjectId | Reference to Employee                 |
| `type`       | Enum     | `SICK`, `CASUAL`, or `ANNUAL`         |
| `startDate`  | Date     | Leave start date                      |
| `endDate`    | Date     | Leave end date                        |
| `reason`     | String   | Reason for leave                      |
| `status`     | Enum     | `PENDING`, `APPROVED`, or `REJECTED`  |

### Payslip
| Field         | Type     | Description                          |
|---------------|----------|--------------------------------------|
| `employeeId`  | ObjectId | Reference to Employee                |
| `month`       | Number   | Payslip month (1–12)                 |
| `year`        | Number   | Payslip year                         |
| `basicSalary` | Number   | Base salary for the period           |
| `allowances`  | Number   | Additional allowances                |
| `deductions`  | Number   | Total deductions                     |
| `netSalary`   | Number   | Final payable amount                 |

---

## Background Jobs (Inngest)

QuickEMS uses [Inngest](https://www.inngest.com/) for event-driven background functions and scheduled cron tasks:

| Job Name                     | Trigger                    | Description                                                             |
|------------------------------|----------------------------|-------------------------------------------------------------------------|
| `auto-check-out`             | `employee/check-out` event | Sends check-out reminder after 9 hrs; auto marks Half Day after 10 hrs  |
| `leave-application-reminder` | `leave/pending` event      | Emails admin if a leave request stays pending for 24 hours              |
| `attendance-reminder-cron`   | Cron: `11:30 AM IST daily` | Finds absent employees (not on leave, not checked in) and emails them   |

To run Inngest locally, start the Inngest Dev Server:

```bash
npx inngest-cli@latest dev
```

---

##  Deployment

Both the client and server are configured for deployment on **Vercel**.

### Backend (Server)

```bash
cd server
vercel --prod
```

The `vercel.json` routes all requests to `server.js` using `@vercel/node`.

### Frontend (Client)

```bash
cd client
vercel --prod
```

The `vercel.json` includes a rewrite rule for client-side routing (SPA fallback).

> **Note:** After deploying the backend, update `VITE_BASE_URL` in the client `.env` to point to your deployed server URL.

---

##  Default Credentials

| Role  | Email                    | Password   |
|-------|--------------------------|------------|
| Admin | *(your `ADMIN_EMAIL`)*   | `admin123` |

> Employees are created by the admin through the dashboard. Each new employee receives a login account automatically.

---

##  Supported Departments

`Engineering` · `Human Resources` · `Marketing` · `Sales` · `Finance` · `Operations` · `IT Support` · `Customer Success` · `Product Management` · `Design`

---

##  License

This project is licensed under the **ISC License**.

---

<p align="center">
  Built with ❤️ using the MERN Stack
</p>
