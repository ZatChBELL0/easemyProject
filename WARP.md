# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a Node.js/Express backend API for a collaborative project management system called "Project Camp". The system supports user authentication, project management, task management with subtasks, and project notes with role-based access control (Admin, Project Admin, Member).

## Development Commands

### Essential Commands
- **Development server**: `npm run dev` (uses nodemon for auto-restart)
- **Production server**: `npm start`
- **Install dependencies**: `npm install`

### Environment Setup
- Copy `.env.example` to `.env` (if exists) and configure:
  - `MONGO_URI` - MongoDB connection string
  - `ACCESS_TOKEN_SECRET` - JWT access token secret
  - `REFRESH_TOKEN_SECRET` - JWT refresh token secret
  - `ACCESS_TOKEN_EXPIRY` - Token expiry time
  - `REFRESH_TOKEN_EXPIRY` - Refresh token expiry time
  - `CORS_ORIGIN` - Allowed CORS origins (comma-separated)
  - `FORGOT_PASSWORD_REDIRECT_URL` - Frontend URL for password reset

## Architecture Overview

### Project Structure
- **Entry Point**: `src/index.js` - Database connection and server startup
- **App Configuration**: `src/app.js` - Express app setup, middleware, and route mounting
- **Database**: `src/db/index.js` - MongoDB connection using Mongoose
- **Models**: `src/models/` - Mongoose schemas (currently User model)
- **Controllers**: `src/controllers/` - Business logic handlers
- **Routes**: `src/routes/` - API endpoint definitions
- **Middlewares**: `src/middlewares/` - Authentication and validation middleware
- **Utils**: `src/utils/` - Helper classes and functions
- **Validators**: `src/validators/` - Input validation using express-validator

### API Architecture
- **Base URL**: `/api/v1/`
- **Authentication**: JWT-based with access/refresh tokens
- **Routes Currently Implemented**:
  - `/api/v1/healthCheck` - System health monitoring
  - `/api/v1/auth` - User authentication and management

### Key Design Patterns

#### Error Handling
- Custom `ApiError` class in `src/utils/api-error.js` for consistent error responses
- `ApiResponse` class in `src/utils/api-response.js` for standardized success responses
- `asyncHandler` wrapper in `src/utils/async-handler.js` for handling async controller errors

#### Authentication Flow
- JWT access tokens (short-lived) and refresh tokens (long-lived)
- Email verification system using temporary tokens
- Password reset via email with secure token generation
- Cookie-based token storage with `httpOnly` and `secure` flags

#### Role-Based Access Control (Planned)
- Three user roles defined in `src/utils/constants.js`:
  - `admin` - Full system access
  - `project_admin` - Project-level administrative access  
  - `member` - Basic project member access

#### Task Management System (Planned)
- Task statuses: `todo`, `in_progress`, `done`
- Hierarchical structure: Projects → Tasks → Subtasks
- File attachment support for tasks

### Database Schema

#### User Model (`src/models/user.models.js`)
- Authentication fields: username, email, password (bcrypt hashed)
- Email verification: `isEmailVerified`, verification tokens
- JWT tokens: `refreshToken` storage
- Password reset: forgot password tokens with expiry
- Avatar support with URL and local path
- Pre-save middleware for password hashing
- Methods for password validation and token generation

### Security Features
- Password hashing with bcrypt (10 rounds)
- JWT-based authentication with separate access/refresh tokens
- Email verification for account activation
- Secure password reset flow with time-limited tokens
- CORS configuration with credential support
- Input validation using express-validator

### Development Notes

#### Middleware Stack Order
1. JSON parsing (`express.json`)
2. URL-encoded parsing (`express.urlencoded`)
3. Static file serving (`express.static`)
4. Cookie parser (`cookie-parser`)
5. CORS configuration
6. Route handlers

#### Validation Pattern
- Validators defined in `src/validators/index.js`
- Applied via middleware chain: `validator()`, `validate`, `controller`
- Uses express-validator for input sanitization and validation

#### Email System
- Email utilities in `src/utils/mail.js` (import present but file not examined)
- Support for verification emails and password reset emails
- Uses mailgen for email template generation

### Future Development Areas
Based on the PRD, the following major components need implementation:
- Project management models and controllers
- Task and subtask management system
- Team member management with role assignments
- Project notes functionality
- File upload handling for task attachments
- Complete API endpoints as defined in PRD.md

### Testing Strategy
- No test framework currently configured
- Consider adding Jest or Mocha for unit/integration testing
- API endpoint testing should cover authentication, authorization, and business logic

### Code Style Notes
- Uses ES6 modules (`import/export`)
- Async/await pattern for asynchronous operations
- Consistent error handling with try-catch and ApiError
- RESTful API design principles
- Separation of concerns: routes → controllers → models