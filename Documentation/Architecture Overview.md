# Architecture Overview

## API Gateway
### Role:
- Serves as the front door for client requests.

### Responsibilities:
- Route requests to the appropriate backend service.
- Perform initial authentication/authorization checks.
- Optionally log access events that can be forwarded for auditing.

---

## Authorization Provider Service
### Role:
- Handles user authentication, token issuance (using Keycloak or Spring Authorization Server), and role management.

### Events Raised:
- **UserRegisteredEvent**: When a new user is created.
- **UserLoggedInEvent**: Upon successful authentication.
- **UserLoggedOutEvent**: When a user logs out.
- **MFAEvent**: If multi-factor authentication is used (e.g., MFAEnabled, MFASuccess).

---

## Resource Service
### Role:
- Secures and provides access to protected business resources/data.

### Responsibilities:
- Validate the access token.
- Serve data only to authorized users.

### Events Raised:
- **ResourceAccessedEvent**: To log significant data access operations (optional for auditing).

---

## Profile Management Service
### Role:
- Manages user profile details such as personal information, addresses, and preferences.

### Events Raised:
- **ProfileUpdatedEvent**: When a user updates their profile.
- **ProfileCreatedEvent**: On initial profile setup (if separated from registration).

---

## Email and Number Verification Service
### Role:
- Manages verification of email addresses and phone numbers.

### Events Raised:
- **VerificationRequestedEvent**: When a verification is initiated.
- **VerificationCompletedEvent**: Once the verification is successful.

---

## Audit and Logging Service
### Role:
- Collects audit logs and tracks events across the system for security and compliance.

### Responsibilities:
- Subscribe to events published by all other services.
- Persist and analyze audit events.

### Events Raised:
- This service primarily acts as a sink, receiving events (audit logs) rather than raising its own.

---

# Central Event Bus

## Message Broker:
- Use a message broker like **Apache Kafka** or **RabbitMQ** to serve as your event bus.

## Integration:
- Each microservice publishes events to the broker.
- The **Audit and Logging Service**, among others, subscribes to these events to perform its functions.

## Event Contracts:
- Define a consistent event schema (e.g., **JSON** or **Avro**) to ensure interoperability.
- For instance, a **UserRegisteredEvent** might include fields like `userId`, `timestamp`, and `source service`.
