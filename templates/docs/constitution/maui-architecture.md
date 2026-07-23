# Clean Architecture Guidelines for .NET MAUI

## 1. Architectural Foundation
The application strictly adheres to **Clean Architecture** principles, ensuring separation of concerns, testability, and framework independence.

* **Technology Stack:** Optimized for **.NET 10** and **C# 14**, leveraging modern language features for concise, safe, and performant code.
* **Reference Pattern:** Modeled after established enterprise `.NET MAUI` Clean Architecture templates, adapted for strict dependency inversion.

## 2. The Dependency Rule (The Golden Rule)
Source code dependencies must point **inward only**, toward higher-level policies (the Domain). Inner circles dictate interfaces; outer circles implement them.

* **Domain (Core Logic):** The center of the architecture. Contains no references to any other layer.
* **Application (Use Cases):** Contains business use cases and application logic (e.g., MediatR pipelines, CQRS patterns, Interface definitions). Depends strictly on the Domain layer.
* **Infrastructure (Data/External):** Contains implementations of interfaces defined in the Application layer (e.g., SQLite repositories, EF Core DbContexts, external API clients). Depends on the Application layer.
* **Presentation (MAUI UI):** Contains XAML, Pages, and ViewModels. Depends only on the Application layer to execute use cases.

## 3. Presentation & Infrastructure Guardrails
* **UI Logic Isolation:** ViewModels must not contain core business rules. They act solely as adapters between the Application layer's Data Transfer Objects (DTOs) and the MAUI Views.
* **Infrastructure Interchangeability:** The database (SQLite) or external APIs must be treated as plugins. Changing the persistence mechanism should require zero changes to the Domain or Application layers.