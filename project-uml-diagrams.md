# Project UML Diagrams

## 1. System Overview

### High-Level System Architecture
```mermaid
classDiagram
    class AuthenticationSystem {
        +Frontend
        +Backend
        +Database
        +Security
    }
    
    class Frontend {
        +React Components
        +State Management
        +API Client
    }
    
    class Backend {
        +Express Server
        +Auth Service
        +User Service
    }
    
    class Database {
        +MySQL
        +Connection Pool
        +Migrations
    }
    
    class Security {
        +JWT
        +Encryption
        +Rate Limiting
    }
    
    AuthenticationSystem --> Frontend
    AuthenticationSystem --> Backend
    AuthenticationSystem --> Database
    AuthenticationSystem --> Security
```

## 2. Frontend Architecture

### Component Structure
```mermaid
classDiagram
    class App {
        +Router
        +AuthProvider
    }
    
    class AuthProvider {
        +user: User
        +token: string
        +login()
        +logout()
        +register()
    }
    
    class LoginPage {
        +email: string
        +password: string
        +handleSubmit()
        +validateForm()
    }
    
    class SignupPage {
        +name: string
        +email: string
        +password: string
        +confirmPassword: string
        +handleSubmit()
        +validateForm()
    }
    
    class ProtectedRoute {
        +component: ReactComponent
        +checkAuth()
    }
    
    App --> AuthProvider
    AuthProvider --> LoginPage
    AuthProvider --> SignupPage
    AuthProvider --> ProtectedRoute
```

## 3. Backend Architecture

### Service Layer
```mermaid
classDiagram
    class AuthController {
        +register(req, res)
        +login(req, res)
        +logout(req, res)
        +validateToken(req, res)
    }
    
    class AuthService {
        +registerUser(userData)
        +loginUser(credentials)
        +validateToken(token)
        +refreshToken(token)
    }
    
    class UserService {
        +getUserById(id)
        +updateUser(id, data)
        +deleteUser(id)
        +getUserByEmail(email)
    }
    
    class TokenService {
        +generateToken(user)
        +verifyToken(token)
        +refreshToken(token)
    }
    
    class DatabaseService {
        +executeQuery(query, params)
        +beginTransaction()
        +commit()
        +rollback()
    }
    
    AuthController --> AuthService
    AuthService --> UserService
    AuthService --> TokenService
    AuthService --> DatabaseService
```

## 4. Database Schema

### Entity Relationship Diagram
```mermaid
erDiagram
    users ||--o{ sessions : has
    users {
        int id PK
        string name
        string email UK
        string password
        datetime created_at
        datetime updated_at
    }
    sessions {
        int id PK
        int user_id FK
        string token
        datetime expires_at
        datetime created_at
    }
```

## 5. Authentication Flow

### Login Sequence
```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant AuthService
    participant Database
    participant TokenService

    User->>Frontend: Enter credentials
    Frontend->>API: POST /api/auth/login
    API->>AuthService: loginUser(credentials)
    AuthService->>Database: Get user by email
    Database-->>AuthService: User data
    AuthService->>TokenService: Generate JWT
    TokenService-->>AuthService: Token
    AuthService-->>API: Auth response
    API-->>Frontend: 200 OK + token
    Frontend->>Frontend: Store token
    Frontend-->>User: Redirect to dashboard
```

### Registration Sequence
```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant AuthService
    participant Database
    participant EmailService

    User->>Frontend: Fill registration form
    Frontend->>API: POST /api/auth/register
    API->>AuthService: registerUser(userData)
    AuthService->>Database: Check email exists
    Database-->>AuthService: Email available
    AuthService->>Database: Create user
    Database-->>AuthService: User created
    AuthService->>EmailService: Send verification email
    EmailService-->>AuthService: Email sent
    AuthService-->>API: Success response
    API-->>Frontend: 201 Created
    Frontend-->>User: Show success message
```

## 6. State Management

### Authentication State
```mermaid
stateDiagram-v2
    [*] --> Unauthenticated
    Unauthenticated --> Authenticating: Login attempt
    Authenticating --> Authenticated: Success
    Authenticating --> Unauthenticated: Failure
    Authenticated --> Unauthenticated: Logout
    Authenticated --> TokenExpired: Token expires
    TokenExpired --> Authenticated: Refresh token
    TokenExpired --> Unauthenticated: Logout
```

## 7. Use Cases

### Authentication System Use Cases
```mermaid
graph TD
    subgraph Actors
        A[User]
        B[Admin]
        C[System]
    end

    subgraph User Use Cases
        A --> D[Register Account]
        A --> E[Login]
        A --> F[Logout]
        A --> G[Reset Password]
        A --> H[Update Profile]
        A --> I[View Profile]
        A --> J[Change Password]
    end

    subgraph Admin Use Cases
        B --> K[View User List]
        B --> L[Manage User Accounts]
        B --> M[View System Logs]
        B --> N[Block User]
        B --> O[Unblock User]
        B --> P[View User Activity]
    end

    subgraph System Use Cases
        C --> Q[Validate Credentials]
        C --> R[Generate Tokens]
        C --> S[Send Emails]
        C --> T[Log Activities]
        C --> U[Monitor Security]
        C --> V[Backup Data]
    end

    style A fill:#f9f,stroke:#333,stroke-width:2px
    style B fill:#bbf,stroke:#333,stroke-width:2px
    style C fill:#bfb,stroke:#333,stroke-width:2px
```

## 8. Security Architecture

### Security Components
```mermaid
classDiagram
    class SecurityLayer {
        +JWTService
        +EncryptionService
        +RateLimiter
    }
    
    class JWTService {
        +generateToken(user)
        +verifyToken(token)
        +refreshToken(token)
    }
    
    class EncryptionService {
        +hashPassword(password)
        +comparePassword(password, hash)
        +encryptData(data)
        +decryptData(data)
    }
    
    class RateLimiter {
        +checkLimit(ip)
        +incrementAttempts(ip)
        +resetAttempts(ip)
    }
    
    SecurityLayer --> JWTService
    SecurityLayer --> EncryptionService
    SecurityLayer --> RateLimiter
```

## 9. Error Handling

### Error Class Hierarchy
```mermaid
classDiagram
    class Error {
        <<abstract>>
        +message: string
        +code: string
        +timestamp: DateTime
    }
    
    class AuthError {
        +errorType: string
        +attempts: number
    }
    
    class ValidationError {
        +field: string
        +validationRule: string
    }
    
    class DatabaseError {
        +query: string
        +errorCode: number
    }
    
    Error <|-- AuthError
    Error <|-- ValidationError
    Error <|-- DatabaseError
```

## 10. Deployment Architecture

### Production Environment
```mermaid
graph TD
    subgraph "AWS Cloud"
        A[Route 53] --> B[CloudFront]
        B --> C[Load Balancer]
        C --> D[Frontend Servers]
        C --> E[Backend Servers]
        
        D --> F[API Gateway]
        E --> F
        
        F --> G[Database Master]
        G --> H[Database Replica]
        
        subgraph "Monitoring"
            I[CloudWatch]
            J[ELK Stack]
            K[Prometheus]
        end
        
        E --> I
        G --> J
        H --> K
    end
```

## 2. Class Diagrams

### Core Authentication Classes
```mermaid
classDiagram
    class User {
        +id: string
        +name: string
        +email: string
        +password: string
        +createdAt: Date
        +updatedAt: Date
        +isActive: boolean
        +lastLogin: Date
        +verifyPassword()
        +updateProfile()
        +changePassword()
    }
    
    class AuthService {
        +registerUser()
        +loginUser()
        +logoutUser()
        +resetPassword()
        +validateToken()
        +refreshToken()
    }
    
    class TokenService {
        +generateToken()
        +verifyToken()
        +refreshToken()
        +revokeToken()
        +getTokenPayload()
    }
    
    class EmailService {
        +sendVerificationEmail()
        +sendPasswordResetEmail()
        +sendWelcomeEmail()
        +sendSecurityAlert()
    }
    
    class SecurityService {
        +hashPassword()
        +comparePassword()
        +encryptData()
        +decryptData()
        +checkRateLimit()
    }
    
    User --> AuthService
    AuthService --> TokenService
    AuthService --> EmailService
    AuthService --> SecurityService
```

### Frontend Component Classes
```mermaid
classDiagram
    class AuthContext {
        +user: User
        +token: string
        +isAuthenticated: boolean
        +login()
        +logout()
        +register()
        +resetPassword()
    }
    
    class LoginForm {
        +email: string
        +password: string
        +errors: Error[]
        +handleSubmit()
        +validateForm()
        +showErrors()
    }
    
    class SignupForm {
        +name: string
        +email: string
        +password: string
        +confirmPassword: string
        +errors: Error[]
        +handleSubmit()
        +validateForm()
        +showErrors()
    }
    
    class ProtectedRoute {
        +component: ReactComponent
        +checkAuth()
        +redirectToLogin()
    }
    
    AuthContext --> LoginForm
    AuthContext --> SignupForm
    AuthContext --> ProtectedRoute
```

## 3. Sequence Diagrams

### User Registration Flow
```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant AuthService
    participant Database
    participant EmailService
    participant SecurityService

    User->>Frontend: Fill registration form
    Frontend->>SecurityService: Validate password strength
    SecurityService-->>Frontend: Password valid
    
    Frontend->>API: POST /api/auth/register
    API->>AuthService: registerUser(userData)
    
    AuthService->>Database: Check email exists
    Database-->>AuthService: Email available
    
    AuthService->>SecurityService: Hash password
    SecurityService-->>AuthService: Hashed password
    
    AuthService->>Database: Create user
    Database-->>AuthService: User created
    
    AuthService->>EmailService: Send verification email
    EmailService-->>AuthService: Email sent
    
    AuthService-->>API: Success response
    API-->>Frontend: 201 Created
    Frontend-->>User: Show success message
```

### Password Reset Flow
```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant AuthService
    participant Database
    participant EmailService
    participant TokenService

    User->>Frontend: Request password reset
    Frontend->>API: POST /api/auth/reset-password-request
    API->>AuthService: requestPasswordReset(email)
    
    AuthService->>Database: Get user by email
    Database-->>AuthService: User found
    
    AuthService->>TokenService: Generate reset token
    TokenService-->>AuthService: Reset token
    
    AuthService->>EmailService: Send reset email
    EmailService-->>AuthService: Email sent
    
    AuthService-->>API: Success response
    API-->>Frontend: 200 OK
    Frontend-->>User: Show email sent message
    
    User->>Frontend: Submit new password
    Frontend->>API: POST /api/auth/reset-password
    API->>AuthService: resetPassword(token, newPassword)
    
    AuthService->>TokenService: Verify reset token
    TokenService-->>AuthService: Token valid
    
    AuthService->>Database: Update password
    Database-->>AuthService: Password updated
    
    AuthService-->>API: Success response
    API-->>Frontend: 200 OK
    Frontend-->>User: Show success message
```

## 4. Activity Diagrams

### User Authentication Flow
```mermaid
flowchart TD
    A[Start] --> B[User attempts login]
    B --> C{Valid email format?}
    C -->|No| D[Show email format error]
    C -->|Yes| E[Check user exists]
    E --> F{User exists?}
    F -->|No| G[Show user not found error]
    F -->|Yes| H[Validate password]
    H --> I{Valid password?}
    I -->|No| J[Show invalid password error]
    J --> K[Increment failed attempts]
    K --> L{Too many attempts?}
    L -->|Yes| M[Lock account]
    M --> N[Send security alert]
    L -->|No| O[Stop]
    I -->|Yes| P[Generate JWT token]
    P --> Q[Store session]
    Q --> R[Update last login]
    R --> S[Redirect to dashboard]
    D --> O
    G --> O
    N --> O
```

### Account Recovery Flow
```mermaid
flowchart TD
    A[Start] --> B[User requests password reset]
    B --> C{Valid email?}
    C -->|No| D[Show invalid email error]
    C -->|Yes| E[Generate reset token]
    E --> F[Store token in database]
    F --> G[Send reset email]
    G --> H[User clicks reset link]
    H --> I{Token valid?}
    I -->|No| J[Show invalid token error]
    I -->|Yes| K[Show password reset form]
    K --> L[User submits new password]
    L --> M{Password meets requirements?}
    M -->|No| N[Show password requirements error]
    M -->|Yes| O[Update password]
    O --> P[Invalidate reset token]
    P --> Q[Send confirmation email]
    Q --> R[Redirect to login]
    D --> S[Stop]
    J --> S
    N --> S
    R --> S
``` 