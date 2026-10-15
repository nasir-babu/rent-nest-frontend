# Rent Nest Frontend

**A rental marketplace for discovering properties and managing the rental journey from request to payment.**

Rent Nest is a Next.js frontend for tenants, landlords, and administrators. It provides public property discovery and role-specific tools for managing rental requests, listings, payments, and platform users. The application connects to a separate backend API; it does not include that backend.

## Contents

- [Overview](#overview)
- [Features](#features)
- [Screenshots / UI Preview](#screenshots--ui-preview)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Available Scripts](#available-scripts)
- [Application Routes](#application-routes)
- [Project Structure](#project-structure)
- [Backend Integration](#backend-integration)
- [Author and Contact](#author-and-contact)

## Overview

Finding a rental and coordinating the steps around it can involve separate, disconnected processes. Rent Nest brings property search, rental requests, landlord listing management, and tenant payment history into one web application, with different dashboards for each user role.

The frontend relies on a separately hosted backend for authentication, property and rental data, administration, and payment-session operations. A reachable backend with the expected API routes is required for the application to work beyond the UI.

## Features

- **Property discovery:** Browse featured listings, search and filter properties, and view individual property details, amenities, price, category, and availability.
- **Authentication:** Register and log in through the backend; validated forms use server actions, and successful logins are routed to the appropriate role dashboard.
- **Tenant tools:** Submit and track rental requests, view payment history, initiate payment for eligible rentals, and submit a review for a completed rental.
- **Landlord tools:** View and manage property listings, create and edit properties, delete listings, and review incoming rental requests by updating their status.
- **Admin tools:** View platform users, properties, and rental requests; manage user ban status; and inspect listings and rental requests on the moderation page.
- **Shared interface:** Public navigation and footer, reusable form and UI components, loading and error states, and toast notifications.

## Screenshots / UI Preview

Add current application screenshots in place of the placeholder URL:

![Your picture link](https://res.cloudinary.com/xhmxwbu4/image/upload/v1791970207/screencapture-rentnest-frontend-22-vercel-app-2026-10-14-15_31_49.jpg)

## Tech Stack

### Framework and language

- Next.js 16 (App Router)
- React 19
- TypeScript 5

### Styling and UI

- Tailwind CSS 4
- Base UI React
- shadcn tooling and locally maintained UI components
- Lucide React and React Icons
- Sonner notifications
- `next-themes`, `clsx`, `tailwind-merge`, `class-variance-authority`, and `tw-animate-css`

### Forms, validation, and integrations

- React Hook Form with `@hookform/resolvers`
- Zod
- Fetch-based integration with the Rent Nest backend API
- Stripe.js and React Stripe.js dependencies for payment-related integration
- `jsonwebtoken` for JWT-related token handling

### Development tools

- ESLint 9 with `eslint-config-next`
- PostCSS with `@tailwindcss/postcss`

## Getting Started

### Prerequisites

- Node.js **20.9 or newer**
- npm (the repository includes `package-lock.json`)
- Access to a running Rent Nest backend API

### Installation and local development

1. Clone the repository and enter the project directory:

   ```bash
   git clone <repository-url>
   cd rent_nest_frontend
   ```

2. Install the locked dependencies:

   ```bash
   npm ci
   ```

3. Create `.env.local` in the project root and configure the variables described below.

4. Start the development server:

   ```bash
   npm run dev
   ```

5. Open [http://localhost:3000](http://localhost:3000).

The backend must be running and reachable at `NEXT_PUBLIC_BACKEND_URL`. Its API routes and response shapes must match those used by this frontend.

## Environment Variables

Create a `.env.local` file in the project root. The following variables are referenced by the application:

```env
# Backend origin; API paths such as /api/auth/login are appended by the frontend.
NEXT_PUBLIC_BACKEND_URL=http://localhost:5000

# JWT secrets used by the proxy to verify access and refresh tokens.
# Configure these to match the backend's signing secrets. Keep them private.
JWT_ACCESS_SECRET=replace-with-your-access-token-secret
JWT_REFRESH_SECRET=replace-with-your-refresh-token-secret
```

`NEXT_PUBLIC_BACKEND_URL` is included in the client-side environment by Next.js, so use an appropriate API origin and do not put secrets in it. JWT secrets should remain server-side and must not be committed. The sample `.env.example` also lists `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`; the current source does not reference that variable directly.

## Available Scripts

| Command         | Description                                                    |
| --------------- | -------------------------------------------------------------- |
| `npm run dev`   | Start the Next.js development server.                          |
| `npm run build` | Create a production build.                                     |
| `npm run start` | Serve the production build locally. Run `npm run build` first. |
| `npm run lint`  | Run ESLint.                                                    |

## Application Routes

| Route                                     | Purpose                                                |
| ----------------------------------------- | ------------------------------------------------------ |
| `/`                                       | Landing page with featured properties and search.      |
| `/properties`                             | Browse and filter property listings.                   |
| `/properties/[id]`                        | View a property and access its rental request flow.    |
| `/login`, `/register`                     | Sign in or create an account.                          |
| `/tenant-dashboard`                       | Tenant overview, rental requests, and payment history. |
| `/tenant-dashboard/requests`              | Tenant rental request list.                            |
| `/tenant-dashboard/payment`               | Tenant payment history.                                |
| `/landlord-dashboard`                     | Landlord overview and property management.             |
| `/landlord-dashboard/properties`          | Manage landlord property listings.                     |
| `/landlord-dashboard/properties/new`      | Create a property listing.                             |
| `/landlord-dashboard/properties/requests` | Review incoming rental requests.                       |
| `/admin-dashboard`                        | Administrator overview.                                |
| `/admin-dashboard/users`                  | Manage platform users and ban status.                  |
| `/admin-dashboard/moderation`             | Inspect property listings and rental requests.         |
| `/payment/success`, `/payment/cancel`     | Payment flow result pages.                             |

Route groups such as `(publicGroup)` and `(dashboardGroup)` are organizational App Router folders and do not appear in the public URL paths.

## Project Structure

```text
app/
  (authGroup)/                 Login, registration, actions, and form components
  (dashboardGroup)/            Admin, landlord, and tenant dashboards
    _actions/                   Dashboard server actions
    _components/                Shared dashboard components
  (publicGroup)/                Landing page and property browsing routes
    _components/                Public search and rental-request components
  payment/                      Payment success and cancellation pages
  globals.css                   Global styles
components/
  share/                        Shared navigation, footer, and property filters
  ui/                           Reusable UI primitives
lib/                             Shared library helpers
schema/                          Zod validation schemas
services/                        Backend API service functions
types/                            Shared TypeScript types
utils/                            Formatting, JWT, and error helpers
public/                           Static assets
proxy.ts                          Request proxy and token handling
```

## Backend Integration

Backend calls are grouped under `services/` and use `NEXT_PUBLIC_BACKEND_URL` as their base origin. Authentication actions are in `app/(authGroup)/_actions/`; the project also includes an API integration map in [`API_INTEGRATION.md`](./API_INTEGRATION.md).

The implemented API areas include:

- Authentication and user profile: `/api/auth/*`
- Property listings and categories: `/api/properties` and `/api/categories`
- Rental requests and reviews: `/api/rentals` and `/api/reviews`
- Landlord listings and incoming requests: `/api/landlord/*`
- Administration: `/api/admin/*`
- Payments: `/api/payments` and `/api/payments/create`

Run the backend separately and configure its origin before using features that fetch or modify application data.

## 👨‍💻 Author & Contact

**Nasirullah Babu**  
_Full Stack Web Developer | Web Solution Specialist_

- 🌐 **Portfolio:** [https://nasirullah.vercel.app/](https://nasirullah.vercel.app/)
- 💼 **LinkedIn:** [Nasirullah Babu](https://linkedin.com/in/nasirullah-babu-8342531b8)
- 📧 **Email:** nasir21b@gmail.com
