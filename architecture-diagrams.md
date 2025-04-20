# Project Architecture and UML Diagrams

## System Architecture Overview

### High-Level Architecture
```mermaid
graph TD
    subgraph "Client Layer"
        A[Web Browser] --> B[React Application]
        B --> C[Auth Components]
        B --> D[State Management]
    end

    subgraph "API Layer"
        E[API Gateway] --> F[Load Balancer]
        F --> G[Backend Servers]
    end

    subgraph "Service Layer"
        G --> H[Auth Service]
        G --> I[User Service]
        H --> J[Database Service]
    end

    subgraph "Data Layer"
        J --> K[MySQL Database]
        K --> L[Database Replica]
    end

    subgraph "Security Layer"
        M[JWT Service]
        N[Encryption Service]
        O[Rate Limiter]
    end

    C --> E
    H --> M
    H --> N
    E --> O
```

## Component Architecture

### Frontend Components
```mermaid
classDiagram
    class AuthContext {
        +user: User
        +token: string
        +isAuthenticated: boolean
        +login(email, password)
        +logout()
        +register(userData)
    }

    class LoginComponent {
        +email: string
        +password: string
        +handleSubmit()
        +validateForm()
    }

    class SignupComponent {
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

    AuthContext --> LoginComponent
    AuthContext --> SignupComponent
    AuthContext --> ProtectedRoute
```

### Backend Components
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
    }

    class DatabaseService {
        +executeQuery(query, params)
        +beginTransaction()
        +commit()
        +rollback()
    }

    AuthController --> AuthService
    AuthService --> UserService
    AuthService --> DatabaseService
    UserService --> DatabaseService
```

## Sequence Diagrams

### User Registration Flow
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

### User Login Flow
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

## Use Case Diagrams

### Authentication Use Cases
```mermaid
useCaseDiagram
    actor User
    actor Admin
    actor System

    User --> (Register Account)
    User --> (Login)
    User --> (Logout)
    User --> (Reset Password)
    User --> (Update Profile)
    
    Admin --> (View User List)
    Admin --> (Manage User Accounts)
    Admin --> (View System Logs)
    
    System --> (Validate Credentials)
    System --> (Generate Tokens)
    System --> (Send Emails)
    System --> (Log Activities)
```

## Activity Diagrams

### Authentication Flow
```mermaid
activityDiagram
    start
    :User attempts to access protected resource;
    
    if (Has valid token?) then (yes)
        :Allow access;
    else (no)
        :Redirect to login;
        :User enters credentials;
        
        if (Valid credentials?) then (yes)
            :Generate JWT token;
            :Store token in localStorage;
            :Redirect to requested resource;
        else (no)
            :Show error message;
            :Return to login form;
        endif
    endif
    
    stop
```

### Password Reset Flow
```mermaid
activityDiagram
    start
    :User requests password reset;
    :System generates reset token;
    :Send email with reset link;
    
    :User clicks reset link;
    if (Token valid?) then (yes)
        :Show password reset form;
        :User enters new password;
        :System updates password;
        :Show success message;
    else (no)
        :Show invalid token message;
    endif
    
    stop
```

## State Diagrams

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

## Deployment Architecture

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

## Security Architecture

### Security Layers
```mermaid
graph TD
    subgraph "Application Security"
        A[Input Validation] --> B[Request Sanitization]
        B --> C[Authentication]
        C --> D[Authorization]
    end
    
    subgraph "Network Security"
        E[HTTPS/TLS] --> F[Firewall]
        F --> G[WAF]
        G --> H[Rate Limiting]
    end
    
    subgraph "Data Security"
        I[Encryption at Rest] --> J[Encryption in Transit]
        J --> K[Access Control]
        K --> L[Audit Logging]
    end
    
    A --> E
    D --> I
``` 