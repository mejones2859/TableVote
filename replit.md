# TableVote - Group Restaurant Decision App

## Overview

TableVote is a collaborative web application that helps groups decide where to eat. Users can create or join groups, submit their food preferences (cuisine types, dietary restrictions, price range, distance, ratings), receive restaurant recommendations, and make group decisions through voting or random selection. The app uses Replit Auth for authentication and PostgreSQL for data persistence.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend Architecture
- **Framework**: React 18 with TypeScript
- **Routing**: Wouter for client-side routing
- **State Management**: TanStack React Query for server state and caching
- **UI Components**: shadcn/ui component library built on Radix UI primitives
- **Styling**: Tailwind CSS with custom theme configuration supporting light/dark modes
- **Build Tool**: Vite for development and production builds
- **Animations**: Framer Motion for UI transitions

### Backend Architecture
- **Framework**: Express.js with TypeScript
- **Authentication**: Replit Auth via OpenID Connect with Passport.js
- **Session Management**: express-session with PostgreSQL session store (connect-pg-simple)
- **API Design**: RESTful endpoints under `/api/` prefix
- **Restaurant Data**: Google Places API (New) via `server/googlePlaces.ts` - Text Search endpoint for real restaurant data
- **Development**: Vite middleware integration for hot module replacement

### Data Storage
- **Database**: PostgreSQL with Drizzle ORM
- **Schema Location**: `shared/schema.ts` defines all database tables
- **Key Tables**:
  - `users` and `sessions` - Authentication (required for Replit Auth)
  - `groups` - Group sessions with invite codes
  - `group_members` - Group membership tracking
  - `preferences` - User food preferences per group (includes location: latitude, longitude, locationName)
  - `restaurants` - Restaurant data
  - `recommendations` - Generated restaurant suggestions
  - `votes` - User votes on recommendations
  - `decisions` - Final group decisions

### Authentication Flow
- Replit Auth handles user identity via OIDC
- Sessions stored in PostgreSQL with 1-week TTL
- `isAuthenticated` middleware protects API routes
- User data synced to database on login via `upsertUser`

### Project Structure
```
├── client/           # React frontend
│   └── src/
│       ├── components/  # UI components (shadcn/ui)
│       ├── hooks/       # Custom React hooks
│       ├── lib/         # Utilities and API client
│       └── pages/       # Route pages
├── server/           # Express backend
│   ├── replit_integrations/auth/  # Auth system
│   ├── routes.ts     # API endpoints
│   └── storage.ts    # Database operations
├── shared/           # Shared types and schema
│   ├── schema.ts     # Drizzle database schema
│   └── models/       # Type definitions
└── migrations/       # Database migrations
```

## External Dependencies

### Database
- **PostgreSQL**: Primary database via `DATABASE_URL` environment variable
- **Drizzle ORM**: Type-safe database queries and schema management
- **drizzle-kit**: Database migration tooling (`db:push` command)

### Authentication
- **Replit Auth**: OpenID Connect provider for user authentication
- **Environment Variables Required**:
  - `DATABASE_URL` - PostgreSQL connection string
  - `SESSION_SECRET` - Express session secret
  - `GOOGLE_PLACES_API_KEY` - Google Places API (New) key for restaurant search
  - `ISSUER_URL` - Replit OIDC issuer (defaults to https://replit.com/oidc)
  - `REPL_ID` - Replit environment identifier

### Key NPM Packages
- `@tanstack/react-query` - Data fetching and caching
- `openid-client` + `passport` - Authentication handling
- `zod` + `drizzle-zod` - Runtime validation
- `framer-motion` - Animation library
- Full Radix UI primitive collection for accessible components