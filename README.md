# Installment Tracker

## Table of Contents

- [Project Description](#project-description)
- [Tech Stack](#tech-stack)
- [Features](#features)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Environment Variables](#environment-variables)
  - [Running the Application](#running-the-application)
- [Database Schema](#database-schema)
- [API Routes](#api-routes)
- [State Management](#state-management)
- [Contract Generation](#contract-generation)
- [Additional Information](#additional-information)

## Project Description

Installment Tracker is a web application designed for local businesses to manage and track installment payments. The app offers a dynamic and flexible platform for handling payment schedules, customer information, generating reports, and creating contracts ready for printing and signing.

## Tech Stack

- **Frontend**: TypeScript, Next.js, TailwindCSS, Shadcn
- **Backend**: TypeScript, Next.js API routes
- **Database**: PostgreSQL
- **ORM**: Prisma
- **State Management**: Zustand

## Features

- **User Authentication and Authorization**
  - Sign up, login, and logout
  - Role-based access control (Admin, Manager, Employee, Read-Only)
- **Business Management**
  - Multi-tenancy support
  - CRUD operations for business profiles
  - Assign employees to businesses
- **Customer Management**
  - CRUD operations for customer profiles
  - View customer payment history and installment details
- **Installment Management**
  - Create and manage installment plans
  - Track installment payments
  - Automatic reminders for upcoming payments
- **Reporting**
  - Generate payment history reports
  - View overdue installments and summary statistics
- **Contract Generation**
  - Generate `.docx` documents for contracts
  - Pre-fill customer and business details into templates
  - Downloadable and printable contracts
- **Notifications**
  - Email and SMS notifications for payment reminders and overdue alerts
- **Currency Support**
  - Support for multiple currencies

## Getting Started

### Prerequisites

- Node.js
- PostgreSQL
- Yarn or npm

### Installation

1. Clone the repository:

   ```sh
   git clone https://github.com/yourusername/installment-tracker.git
   cd installment-tracker
   ```

2. Install dependencies:
   ```sh
   yarn install
   # or
   npm install
   ```

### Environment Variables

Create a `.env` file in the root directory and add the following environment variables:

DATABASE_URL=postgresql://USER@HOST/DATABASE
NEXTAUTH_SECRET=your_secret
NEXTAUTH_URL=http://localhost:3000

### Running the Application

1. Run the Prisma migrations to set up the database:

   ```sh
   npx prisma migrate dev
   ```

2. Start the development server:

   ```sh
   yarn dev
   # or
   npm run dev
   ```

3. Open your browser and navigate to `http://localhost:3000`.

## Database Schema

```prisma
model User {
  id          Int      @id @default(autoincrement())
  email       String   @unique
  password    String
  role        Role
  businesses  Business[]
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}

model Business {
  id         Int        @id @default(autoincrement())
  name       String
  ownerId    Int
  owner      User       @relation(fields: [ownerId], references: [id])
  employees  User[]
  customers  Customer[]
  createdAt  DateTime   @default(now())
  updatedAt  DateTime   @updatedAt
}

model Customer {
  id         Int          @id @default(autoincrement())
  name       String
  email      String       @unique
  businessId Int
  business   Business     @relation(fields: [businessId], references: [id])
  installments Installment[]
  createdAt  DateTime     @default(now())
  updatedAt  DateTime     @updatedAt
}

model Installment {
  id         Int        @id @default(autoincrement())
  amount     Float
  currency   String
  dueDate    DateTime
  paid       Boolean    @default(false)
  customerId Int
  customer   Customer   @relation(fields: [customerId], references: [id])
  createdAt  DateTime   @default(now())
  updatedAt  DateTime   @updatedAt
}

enum Role {
  ADMIN
  MANAGER
  EMPLOYEE
  READ_ONLY
}
```

## API Routes

- Auth Routes: /api/auth/\*
- Business Routes: /api/business/\*
- Customer Routes: /api/customer/\*
- Installment Routes: /api/installment/\*
- Report Routes: /api/report/\*

## State Management

Using Zustand for state management.

```ts
import create from "zustand";

interface User {
  id: number;
  email: string;
  role: "ADMIN" | "MANAGER" | "EMPLOYEE" | "READ_ONLY";
}

interface AppState {
  user: User | null;
  setUser: (user: User) => void;
  logout: () => void;
}

const useStore = create<AppState>((set) => ({
  user: null,
  setUser: (user) => set({ user }),
  logout: () => set({ user: null }),
}));

export default useStore;
```

## Contract Generation

Using the docx library for generating .docx files.

```ts
import { Document, Packer, Paragraph, TextRun } from "docx";
import { saveAs } from "file-saver";

const generateContract = (customer, business, contractTemplate) => {
  const doc = new Document({
    sections: [
      {
        properties: {},
        children: [
          new Paragraph({
            children: [
              new TextRun(contractTemplate.title),
              new TextRun({ text: "\n\n", break: 1 }),
              new TextRun(`Customer: ${customer.name}`),
              new TextRun({ text: "\n\n", break: 1 }),
              new TextRun(`Business: ${business.name}`),
              // Add more TextRun instances for other contract details
            ],
          }),
        ],
      },
    ],
  });

  Packer.toBlob(doc).then((blob) => {
    saveAs(blob, "contract.docx");
  });
};
```

## Additional Information

- TailwindCSS Setup: Ensure TailwindCSS is properly set up for styling.
- Documentation: Create comprehensive documentation for - API endpoints, state management, and UI components.
- Testing: Write unit and integration tests for critical components and functionalities.
- Deployment: Plan for deployment (e.g., Vercel for Next.js, Docker for containerization).

For any further questions or contributions, please feel free to create an issue or submit a pull request.

Happy Coding!
