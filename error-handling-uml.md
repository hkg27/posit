# Error Handling in UML Diagrams

## Sequence Diagram with Error Handling

### Login Error Flow
```mermaid
sequenceDiagram
    participant User
    participant Frontend
    participant API
    participant AuthService
    participant Database

    User->>Frontend: Enter credentials
    Frontend->>API: POST /api/auth/login
    API->>AuthService: loginUser(credentials)
    
    alt Invalid Email Format
        AuthService-->>API: Error: Invalid email format
        API-->>Frontend: 400 Bad Request
        Frontend-->>User: Show email error
    else Email Not Found
        AuthService->>Database: Get user by email
        Database-->>AuthService: No user found
        AuthService-->>API: Error: User not found
        API-->>Frontend: 404 Not Found
        Frontend-->>User: Show user not found error
    else Invalid Password
        AuthService->>Database: Get user by email
        Database-->>AuthService: User found
        AuthService->>AuthService: Validate password
        AuthService-->>API: Error: Invalid password
        API-->>Frontend: 401 Unauthorized
        Frontend-->>User: Show password error
    else Server Error
        AuthService->>Database: Get user by email
        Database-->>AuthService: Database error
        AuthService-->>API: Error: Internal server error
        API-->>Frontend: 500 Internal Server Error
        Frontend-->>User: Show server error
    end
```

## Activity Diagram with Error Paths

### Authentication Error Flow
```mermaid
activityDiagram
    start
    :User attempts login;
    
    if (Valid email format?) then (no)
        :Show email format error;
        stop
    else (yes)
        :Check user exists;
    endif
    
    if (User exists?) then (no)
        :Show user not found error;
        stop
    else (yes)
        :Validate password;
    endif
    
    if (Valid password?) then (no)
        :Show invalid password error;
        stop
    else (yes)
        :Generate token;
        try
            :Store token;
        catch (Storage error)
            :Show storage error;
            stop
        end
        :Redirect to dashboard;
    endif
    
    stop
```

## State Diagram with Error States

### Authentication State with Errors
```mermaid
stateDiagram-v2
    [*] --> Unauthenticated
    Unauthenticated --> Authenticating: Login attempt
    Authenticating --> EmailError: Invalid email
    Authenticating --> UserNotFound: Email not found
    Authenticating --> PasswordError: Invalid password
    Authenticating --> ServerError: System error
    Authenticating --> Authenticated: Success
    
    EmailError --> Unauthenticated: Retry
    UserNotFound --> Unauthenticated: Retry
    PasswordError --> Unauthenticated: Retry
    ServerError --> Unauthenticated: Retry
    Authenticated --> Unauthenticated: Logout
```

## Class Diagram with Error Classes

### Error Handling Classes
```mermaid
classDiagram
    class AuthError {
        <<abstract>>
        +message: string
        +code: string
        +timestamp: DateTime
        +getDetails()
    }
    
    class ValidationError {
        +field: string
        +validationRule: string
        +getFieldErrors()
    }
    
    class AuthenticationError {
        +errorType: string
        +attempts: number
        +getErrorType()
    }
    
    class DatabaseError {
        +query: string
        +errorCode: number
        +getQueryDetails()
    }
    
    AuthError <|-- ValidationError
    AuthError <|-- AuthenticationError
    AuthError <|-- DatabaseError
```

## Use Case Diagram with Error Scenarios

### Authentication Error Use Cases
```mermaid
useCaseDiagram
    actor User
    actor System
    
    User --> (Attempt Login)
    User --> (Handle Login Error)
    User --> (Reset Password)
    User --> (Handle Password Reset Error)
    
    System --> (Validate Input)
    System --> (Check User Exists)
    System --> (Verify Password)
    System --> (Generate Token)
    System --> (Handle Database Error)
    System --> (Handle Network Error)
    
    (Attempt Login) ..> (Validate Input) : <<include>>
    (Attempt Login) ..> (Check User Exists) : <<include>>
    (Attempt Login) ..> (Verify Password) : <<include>>
    (Attempt Login) ..> (Generate Token) : <<include>>
    
    (Handle Login Error) ..> (Handle Database Error) : <<extend>>
    (Handle Login Error) ..> (Handle Network Error) : <<extend>>
```

## Component Diagram with Error Handling Components

### Error Handling Architecture
```mermaid
classDiagram
    class ErrorHandler {
        +handleError(error: Error)
        +logError(error: Error)
        +notifyAdmin(error: Error)
    }
    
    class ValidationService {
        +validateEmail(email: string)
        +validatePassword(password: string)
        +validateUserData(data: UserData)
    }
    
    class LoggingService {
        +logError(error: Error)
        +logWarning(warning: string)
        +logInfo(info: string)
    }
    
    class NotificationService {
        +notifyUser(error: Error)
        +notifyAdmin(error: Error)
        +sendAlert(alert: string)
    }
    
    ErrorHandler --> ValidationService
    ErrorHandler --> LoggingService
    ErrorHandler --> NotificationService
```

## Error Response Flow
```mermaid
graph TD
    A[Error Occurs] --> B{Error Type}
    
    B -->|Validation| C[Validation Service]
    B -->|Auth| D[Auth Service]
    B -->|Database| E[Database Service]
    B -->|System| F[System Service]
    
    C --> G[Error Handler]
    D --> G
    E --> G
    F --> G
    
    G --> H[Logging Service]
    G --> I[Notification Service]
    
    H --> J[Error Response]
    I --> J
    
    J --> K[Client]
```

## Best Practices for Error Handling in UML
1. Always show error paths in sequence diagrams
2. Include error states in state diagrams
3. Document error classes and hierarchies
4. Show error handling components
5. Include error scenarios in use cases
6. Document error response flows
7. Show error recovery paths
8. Include error logging and notification
9. Document error codes and messages
10. Show error handling at system boundaries 