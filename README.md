# Universal Productivity Hub for Bug Tracking 
#### (pronounced upbeat)
### AI-Assisted Roadmap

`"It doesn't matter if a cat is black or white, if it catches mice it's a good cat"`

---

## **Overall Project Goal:**

To develop a comprehensive bug and project tracking system comprising a robust .NET Web API backend and two distinct Single Page Application (SPA) frontends (Angular and React), serving as a significant upskilling exercise.

---

## **Overall Technology Stack:**

* **Backend:** .NET Web API (latest LTS \- .NET 9\) with C\# 12 and modern syntax (e.g., file-scoped namespaces, Nullable Reference Types). The backend will strictly adhere to a **Rich Domain Model** design, where **domain entities are pure Plain Old C\# Objects (POCOs), completely persistence-ignorant, with no Entity Framework Core specific attributes or interfaces**. All ORM mapping, including properties like `RowVersion` for optimistic concurrency, will be configured exclusively in the `Uphbt.Data` project using **Entity Framework Core's Fluent API**.  
  * **OpenAPI / Scalar for API Documentation**. Business logic will be primarily implemented in the Rich Domain Model layer, with the Service layer handling orchestration and logic that cannot be performed by entities themselves.  
  * **Database:** SQL Server (latest stable version).  
  * **ORM:** Entity Framework Core.  
  * **Authentication:** OpenIddict (for OAuth 2.0 / OpenID Connect with JWTs and Refresh Tokens), ASP.NET Core Identity (for user management), BCrypt.Net-Next for password hashing.  
    * **Token Storage:** Access Token and Refresh Token will be issued by OpenIddict and stored directly in **HttpOnly, Secure, SameSite=Lax (or Strict) cookies** on the client-side. The SPA will not access these tokens via JavaScript directly, but the browser will automatically send them with API requests.  
    * **JWT Payload:** The JWT (Access Token) payload will include standard claims such as `sub` (user ID), `name`, `email`, and critically, **`role` claims for authorization**.  
  * **Real-time Communication:** ASP.NET Core SignalR.  
  * **Internationalization & Localization (i18n/l10n):** Microsoft.Extensions.Localization, Microsoft.AspNetCore.Localization.  
  * **Logging:** Serilog.  
* **Frontend 1 (Angular):** Bug Tracker (Full Features) \- Angular (v20 LTS) \- Using ReactiveFormsModule and Angular Material UI Library (with Proxy for Dev).  
  * **Internationalization & Localization (i18n/l10n):** ngx-translate.  
* **Frontend 2 (React):** Bug Tracker (Full Features \- Mirror of Angular) \- React (v19 LTS) \- Using React Hook Form and Material UI (MUI) Library (with Proxy for Dev).  
  * **Internationalization & Localization (i18n/l10n):** i18next & react-i18next.  
* **HTTPS:** All three components (`uphbt-backend`'s Web API project, `uphbt-angular-frontend`, and `uphbt-react-frontend`) will be configured to use HTTPS for secure communication, both in development and production (where applicable).  
* **Testing:**  
  * **.NET:** xUnit, Moq, Microsoft.AspNetCore.Mvc.Testing.  
  * **Angular:** Jasmine, Karma, Angular Testing Bed, HttpClientTestingModule.  
  * **React:** Jest, React Testing Library, MSW (Mock Service Worker).  
  * **E2E:** Cypress or Playwright.  
* **Deployment (Local/Shareable):** Docker & Docker Compose.  
* **AI Strategy:** Extensive use of LLM for code generation, test scaffolding, cross-framework translation, debugging, and concept explanation. Critical human review and understanding for all AI-generated code.  
* **Source Control:** Git (with a feature branching workflow).  
* **Estimated Timeline:** 4 \- 6 months (aggressive, AI-assisted pace).  
* **Git Workflow Strategy (Feature Branching Model):** For a project with three distinct repositories (.NET Backend, Angular Frontend, React Frontend), a feature branching workflow is highly recommended. This allows independent development and testing of features before merging into stable branches.  
  * **Repository Setup:** Three separate Git repositories:  
    * `uphbt-backend` (.NET Web API)  
    * `uphbt-angular-frontend` (Angular App)  
    * `uphbt-react-frontend` (React App)  
  * Each repository will have at least the following long-lived branches:  
    * `main` (or `master`): Represents the latest production-ready code. Only merged into via pull requests.  
    * `develop`: Integration branch for ongoing development. Features are merged here.  
    * **Feature Branches:** For each new feature, bug fix, or major refactor, create a new branch from `develop`. Naming convention: `feature/your-feature-name`, `bugfix/issue-description`, `refactor/area-name`.  
  * **Development Cycle:**  
    * Pull `develop`: Always start by pulling the latest `develop` branch.  
    * Create Feature Branch: `git checkout -b feature/my-new-feature develop`.  
    * Develop & Commit: Work on your feature. Make frequent, small, atomic commits with clear messages. `git add .`, `git commit -m "feat: implemented user registration form"`.  
    * Push to Remote: Regularly push your feature branch to the remote: `git push origin feature/my-new-feature`.  
    * Keep up with `develop`: Periodically pull `develop` into your feature branch to stay updated and resolve conflicts early: `git pull origin develop` (or `git pull origin develop --rebase` for a cleaner history if preferred).  
  * **Code Review & Merge (Pull Requests):**  
    * When a feature is complete and tested locally, push it to the remote.  
    * Create a Pull Request (PR) from your `feature/branch` to `develop` in your Git hosting platform (e.g., GitHub, GitLab, Azure DevOps).  
    * **Mandatory Code Review:** At least one other team member should review the code.  
    * **Automated Checks:** (Future CI/CD integration) PRs should trigger automated tests and static analysis.  
    * **Merge:** Once approved and all checks pass, merge the feature branch into `develop`. Use "Squash and Merge" or "Rebase and Merge" for a cleaner `develop` history, or "Merge Commit" for retaining all feature branch commits.  
  * **Releases (Optional but Recommended):**  
    * When `develop` reaches a stable point for a release, create a `release/vX.Y.Z` branch from `develop`.  
    * Perform final testing, bug fixes (committed directly to the release branch), and version updates on this branch.  
    * When ready, merge `release/vX.Y.Z` into both `main` and `develop`. Tag `main` with the release version.  
  * **Hotfixes:**  
    * For urgent production bugs, create `hotfix/issue-description` branches directly from `main`.  
    * Once fixed, merge the hotfix branch back into both `main` and `develop`. Tag `main`.  
  * **Important Considerations for Three Repositories:**  
    * **Feature Alignment:** For a single user story (e.g., "Implement Bug Reporting"), you might have a feature branch in each of the three repositories. You'll need to coordinate merging these related branches. A common strategy is to merge the backend feature first, then the frontend(s).  
    * **Version Control for Dependencies:** Ensure `package.json` (Angular/React) and `.csproj` files (.NET) are committed and specify exact (or range-controlled) dependencies.

---

## **High-Level Roadmap:**

### **Phase 1: Core Foundation, Security & i18n Setup (Month 1 \- 1.5)**

**Goal:** Establish a secure, robust backend and foundational authentication/authorization using OpenIddict, with JWTs issued in secure HttpOnly cookies. Set up the basic i18n/l10n framework across all layers and both frontends, using AI to accelerate initial setup.

#### **1.1. Backend (.NET) & Database Setup**

* **Action:**  
  * Initial project setup with a Rich Domain Model design. **All domain entities in `Uphbt.Domain` will be pure Plain Old C\# Objects (POCOs)**. This means they will not contain any Entity Framework Core-specific attributes (e.g., `[Table]`, `[Column]`, `[Key]`, `[Timestamp]`) or implement any EF Core-specific interfaces.  
  * Define internal primary keys for *domain entities* as `long` (mapping to `BIGINT IDENTITY` in SQL Server) and all foreign keys should also be `long`.  
  * Each *domain entity* (e.g., Bug, Comment) should also have a `Guid PublicId` property that is unique and will be exposed to the application layer.  
  * Include a `byte[] RowVersion` property on relevant domain entities (e.g., Bug, and also on the `User` entity within `Uphbt.Identity`) for optimistic concurrency. **The mapping of this `RowVersion` property to the database's `rowversion` or `timestamp` column will be configured exclusively in `Uphbt.Data` using EF Core's Fluent API.**  
  * Layered architecture (`Api`, `Contracts`, Services, Data, Domain, `Identity`), core DB schema (Users).  
  * **EF Core Configuration:** Configure `ApplicationDbContext` and all entity mappings in `Uphbt.Data` using **Fluent API** within the `OnModelCreating` method. This includes defining primary keys, relationships, column types, and specifically, marking the `RowVersion` property for optimistic concurrency using `IsRowVersion()`.  
  * **Authentication & Authorization (SPA-friendly, HttpOnly Cookies):**  
    * Integrate OpenIddict to act as the OAuth 2.0 Authorization Server and OpenID Connect Provider, issuing JWTs (Access and Refresh Tokens).  
    * Configure OpenIddict for the Authorization Code Flow with PKCE for secure SPA authentication.  
    * Enable and configure the Refresh Token Flow within OpenIddict.  
    * Set short lifetimes for Access Tokens (e.g., 5-15 minutes) and longer, but finite, lifetimes for Refresh Tokens (e.g., 7-30 days).  
    * Crucially, OpenIddict's setup will enable Refresh Token Rotation and Reuse Detection when `offline_access` scope is requested, which is vital for detecting and revoking stolen refresh tokens.  
    * **Direct Cookie Storage of Tokens:** Upon successful authentication (after the authorization code exchange), the .NET Backend will issue the **Access Token** and **Refresh Token** directly into **HttpOnly, Secure, SameSite=Lax cookies**.  
      * The Access Token cookie will be relatively short-lived (matching the JWT expiry).  
      * The Refresh Token cookie will be long-lived (matching the JWT expiry).  
      * The JWT (Access Token) payload will include standard claims such as `sub` (user ID), `name`, `email`, and critically, **`role` claims for authorization**.  
      * The frontend (SPA) will **NOT** directly read these tokens via JavaScript. The browser will automatically send these HttpOnly token cookies with every subsequent request to the backend.  
      * The backend will then validate the Access Token from the cookie for each request. If the Access Token is expired but a valid Refresh Token cookie is present, the backend will transparently use the Refresh Token to acquire a new Access Token and Refresh Token, then issue new cookies.  
    * **Custom Login/Logout Endpoints (No Identity UI):** Create custom API endpoints for user login, registration, and logout. These endpoints will interact directly with ASP.NET Core Identity's `UserManager` and `SignInManager` and OpenIddict to manage the authentication flow and issue/clear the token cookies. **No Razor Pages UI provided by ASP.NET Core Identity will be used.**  
    * **Client Configuration:** Dynamically or statically register your Angular and React clients within OpenIddict's configuration/database, specifying `ClientId`, `RedirectUris`, `PostLogoutRedirectUis`, and allowed `GrantTypes`/`Scopes` (including `openid`, `profile`, `email`, **`roles`**, `offline_access`).  
    * Use BCrypt.Net-Next for secure password hashing within ASP.NET Core Identity.  
  * **I18n/L10n:** Set up server-side internationalization using `Microsoft.Extensions.Localization`.  
  * **API Documentation (OpenAPI / Scalar):** Integrate **built-in .NET 9 OpenAPI support** and integrate **Scalar middleware** for API documentation and interactive testing.  
  * **Code Quality:** All C\# code must fully leverage Nullable Reference Types (NRTs) to explicitly define nullability, ensuring properties and method return types reflect whether null is a valid state or value.  
  * **Specific for Identity (with `Uphbt.Identity` Project):**  
    * **`Uphbt.Identity` project creation:** This new project will house your custom `User` entity and related Identity types.  
    * Define the **`User` entity in `Uphbt.Identity`** which **WILL inherit from `IdentityUser<long>`**. This `User` will be the primary representation of a user across your application and identity layers.  
    * Ensure this `User` entity also includes a `Guid PublicId` (for public exposure, distinct from `Id` inherited from `IdentityUser<long>`), `string FullName`, `DateTime? DateHired`, and crucially, a `byte[] RowVersion` property. **This `User` entity will also be a POCO, without EF Core attributes.**  
    * The `Uphbt.Identity` project **will have a package reference to `Microsoft.AspNetCore.Identity`**.  
    * In `Uphbt.Data`, `ApplicationDbContext` will derive from **`IdentityDbContext<User, IdentityRole<long>, long>`** (where `User` now refers to `Uphbt.Identity.User`). This means Identity will use the built-in `IdentityRole<long>` for roles; you will not define a custom `Role` class *unless* you later need to add custom properties to the roles table.  
    * `UserManager<User>` and `SignInManager<User>` (operating on `Uphbt.Identity.User`) will be used by services in `Uphbt.Services` (or dedicated authentication services) to manage users and sign-in operations.  
    * `RoleManager<IdentityRole<long>>` will be used by services in `Uphbt.Services` to manage roles (creating, assigning, etc.).  
    * **Role Seeding:** Pre-seed the roles "user" and "admin" using `RoleManager<IdentityRole<long>>` in `Program.cs` (or a dedicated data seeding class) during application startup.  
  * **Initial Migrations:** Generate and apply the initial Entity Framework Core database migration for Identity and core application entities.  
  * **HTTPS Configuration:** Configure the `uphbt-backend`'s **Web API project to run using HTTPS**, ensuring secure communication for all endpoints, especially for development and local testing.  
* **Optimal Prompt(s):**  
  * "Provide the `dotnet CLI` commands to generate a .NET 9 solution named 'Uphbt'. Then, generate the following projects within this solution, keeping in mind their architectural roles and the adjusted dependency flow:  
    * **`Uphbt.Api` (an HTTPS-based webapi project with controllers. This project serves as the Backend's Presentation Layer, exposing the backend's capabilities via RESTful endpoints. Show how to configure `Microsoft.AspNetCore.OpenApi` and integrate Scalar middleware for .NET 9 in `Program.cs`.)**  
    * **`Uphbt.Contracts`** (a class library, specifically for **Data Transfer Objects (DTOs)** and shared contracts between `Uphbt.Api` and `Uphbt.Services`)  
    * **`Uphbt.Services`** (a class library, representing the application layer, and where **all infrastructure dependency injection, including `ApplicationDbContext` and Identity stores, will be performed**.)  
    * **`Uphbt.Data`** (a class library, responsible for infrastructure/persistence, which will contain **EF Core configuration for persistence-ignorant POCOs and the `ApplicationDbContext` for Identity**)  
    * **`Uphbt.Domain`** (a class library, containing the **pure, rich, domain model entities, value objects, and repository interfaces *for non-Identity domain entities***).  
    * **`Uphbt.Identity`** (a class library, specifically for **custom ASP.NET Core Identity entities like `User` inheriting `IdentityUser<long>`).** After project creation, provide the `dotnet add reference` commands to set up the following clean architecture project references:  
    * `Uphbt.Api` \-\> `Uphbt.Services`  
    * `Uphbt.Api` \-\> `Uphbt.Contracts`  
    * `Uphbt.Services` \-\> `Uphbt.Data`  
    * `Uphbt.Services` \-\> `Uphbt.Domain`  
    * `Uphbt.Services` \-\> `Uphbt.Contracts`  
    * `Uphbt.Services` \-\> `Uphbt.Identity`  
    * `Uphbt.Data` \-\> `Uphbt.Domain`  
    * `Uphbt.Data` \-\> `Uphbt.Identity`  
    * **`Uphbt.Identity` \-\> `Microsoft.AspNetCore.Identity`** (package reference)  
      Ensure the generated solution and projects are compatible with .NET 9 and fully utilize C\# 12 and modern syntax (e.g., file-scoped namespaces, Nullable Reference Types). Crucially, explain that `Uphbt.Data` will have a package reference to `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.SqlServer`."  
  * "Generate a custom `User` entity in C\# for the new **`Uphbt.Identity`** project. It should inherit from `IdentityUser<long>` and include additional application-specific properties like `Guid PublicId`, `string FullName`, `DateTime? DateHired`, and `byte[] RowVersion` for optimistic concurrency. This `User` entity **must be a pure POCO, with no EF Core specific attributes like `[Timestamp]`**."  
  * "Generate the `ApplicationDbContext` for a .NET 9 Web API using EF Core with SQL Server. This `DbContext` should now reside in `Uphbt.Data` and derive from `IdentityDbContext<Uphbt.Identity.User, IdentityRole<long>, long>`. Within its `OnModelCreating` method, demonstrate how to use **Fluent API** to configure the `Uphbt.Identity.User` entity's `RowVersion` property for optimistic concurrency (`IsRowVersion()`) and any other necessary mappings. Explain that `ApplicationDbContext` and Identity stores will be registered for DI in `Uphbt.Services`."  
  * "Provide a C\# extension method `AddUphbtInfrastructure` for `IServiceCollection` to be placed in `Uphbt.Services`. This method should encapsulate all necessary dependency injections for the data layer and application services, including:  
    * Registering `ApplicationDbContext` with `options.UseSqlServer(configuration.GetConnectionString("DefaultConnection"))`.  
    * Configuring ASP.NET Core Identity using `AddIdentity<User, IdentityRole<long>>().AddEntityFrameworkStores<ApplicationDbContext>()`.  
    * Registering concrete repository implementations (e.g., `BugRepository`, `CommentRepository`, `UserRepository`) from `Uphbt.Data`.  
    * Registering application services (e.g., `BugService`, `CommentService`, `UserService`) from `Uphbt.Services`. Explain how this centralizes infrastructure configuration and prevents `Uphbt.Api` from referencing `Uphbt.Data` or `Uphbt.Identity` directly for DI purposes."  
  * "Show me how to set up OpenIddict as an Authorization Server in a .NET 9 Web API using `Program.cs`. Configure it for Authorization Code Flow with PKCE, Refresh Token Flow. Define client applications (e.g., for Angular and React SPAs with specific `ClientId` and `RedirectUris`), set short access token lifetimes and longer refresh token lifetimes. Crucially, demonstrate how to issue both the **Access Token and Refresh Token as HttpOnly, Secure, SameSite=Lax cookies** upon successful authentication, instead of returning them in the response body. Ensure the JWT payload includes `role` claims for authorization (by requesting `roles` scope)."  
  * "Provide a C\# `AuthController` (or `LoginController`) in a .NET 9 Web API that handles user login and registration by interacting indirectly with `UserManager<Uphbt.Identity.User>` and `SignInManager<Uphbt.Identity.User>` via an injected `IUserService` (from `Uphbt.Services`) that accepts/returns DTOs from `Uphbt.Contracts`. This controller should **not** use any built-in .NET Identity UI. After successful login, it should use OpenIddict to issue **HttpOnly, Secure, SameSite=Lax cookies containing the Access Token and Refresh Token**. Include a secure logout endpoint that explicitly clears these cookies and revokes the refresh token if applicable."  
  * "How to implement BCrypt.Net-Next for password hashing within ASP.NET Core Identity in a .NET 9 application, using `Uphbt.Identity.User`."  
  * "Guide me on how to perform initial role seeding for 'user' and 'admin' roles using `RoleManager<IdentityRole<long>>` in `Program.cs` (or a dedicated data seeding class) during application startup, ensuring `RoleManager` is correctly resolved after being configured via `AddUphbtInfrastructure`."  
  * "Generate a basic example of server-side internationalization (i18n) setup in a .NET 9 Web API using `Microsoft.Extensions.Localization` for a `SharedResources.cs` file."  
  * "How to configure **HTTPS for a .NET 9 Web API** in `appsettings.json` and `Program.cs` for development, including setting up a self-signed certificate if necessary for local development."

#### **1.2. Frontend 1 (Angular) \- Core Setup, Security & i18n Integration (uphbt-angular-frontend)**

* **Action:**  
  * Initialize Angular v20 project using Angular CLI.  
  * Integrate Angular Material UI library.  
  * **Authentication & Session Management (SPA-friendly, HttpOnly Cookies):**  
    * Develop a robust `AuthService` to manage the authentication flow.  
    * Implement the Authorization Code Flow with PKCE client-side: redirecting to the OpenIddict `/connect/authorize` endpoint (hosted by your .NET backend), handling the callback with the authorization code.  
    * Crucially, the backend will handle the exchange of the authorization code for JWTs (containing roles) and then issue `HttpOnly, Secure, SameSite` cookies containing the Access and Refresh Tokens to the Angular app.  
    * The Angular app will **NOT** directly read or store JWT Access/Refresh Tokens in JavaScript-accessible storage. All API calls will implicitly rely on the browser sending these HttpOnly token cookies.  
    * Implement custom UI for login (initiating the redirect flow to the backend's authorization endpoint, not Identity's UI), registration (calling the custom backend registration endpoint), logout (clearing local session and calling backend's logout endpoint), and user session management (relying on the backend-issued token cookies).  
    * The Angular app will not implement explicit token refresh logic as this is handled transparently by the backend; if an API call fails with 401, the backend should attempt to refresh. If that fails (e.g., refresh token expired/invalid), the frontend should be redirected to login.  
  * **HTTP Interceptor for API Calls:**  
    * Create an Angular HTTP Interceptor. Its primary role will be to detect 401 Unauthorized responses from the API.  
    * If a 401 occurs, it signifies that the backend-managed token cookies are no longer valid (e.g., due to server-side JWT expiration/revocation). The interceptor will then redirect the user to the login page to re-initiate the authentication flow.  
    * The interceptor will NOT attach JWTs as they are sent via HttpOnly cookies by the browser.  
  * **Routing & Guards:** Implement Angular Router and create route guards to protect authenticated and authorized routes, checking for the presence/validity of the session (e.g., by making a lightweight API call to a `/me` endpoint that relies on the token cookies, and parsing roles from the response to enable client-side role-based rendering).  
  * **Forms:** Utilize Angular's Reactive Forms for login, registration, and user profile forms, with comprehensive client-side validation using `Validators`.  
  * **I18n/L10n:** Integrate `ngx-translate` for frontend internationalization, setting up translation files (JSON) and configuring the translation service.  
  * Configure proxy for development server to handle API calls.  
  * **HTTPS Configuration:** Configure the `uphbt-angular-frontend` development server to serve the application over **HTTPS**, ensuring all development traffic is encrypted.  
* **Optimal Prompt(s):**  
  * "Guide me through setting up an Angular v20 project to implement the client-side of the Authorization Code Flow with PKCE for authentication, where the backend issues Access and Refresh Tokens directly into HttpOnly, Secure, SameSite=Lax cookies, and the JWT payload includes user roles. Show the redirect to the backend's authorization endpoint and handling the callback to complete the flow."  
  * "Provide an Angular `AuthService` example that manages user login (by redirecting to OpenIddict endpoint), registration (by calling a custom backend API), and logout (by calling a custom backend API to clear token cookies and invalidate tokens), without directly reading JWT Access or Refresh Tokens in JavaScript. Show how to retrieve user roles from a secure backend endpoint (e.g., `/me`) after authentication for client-side authorization logic."  
  * "Generate an Angular HTTP Interceptor that detects 401 Unauthorized responses and redirects the user to the login page, since JWTs are managed by the backend via HttpOnly token cookies. Explain why explicit JWT attachment is not needed here."  
  * "How to implement Angular Route Guards to protect routes, by checking the user's authentication status and roles based on a lightweight API call to a `/me` endpoint (which relies on the token cookies and returns user roles)."  
  * "How to integrate `ngx-translate` into an Angular v20 project, including setting up translation files and using the translate pipe/service."  
  * "Show an Angular Reactive Form for user login and registration with client-side validation and Angular Material components."  
  * "How to configure the **Angular CLI development server to serve the application over HTTPS** using a self-signed certificate, and how to configure API proxying for HTTPS backend endpoints."

#### **1.3. Frontend 2 (React) \- Core Setup, Security & i18n Integration (uphbt-react-frontend)**

* **Action:**  
  * Initialize React v19 project.  
  * Integrate Material-UI (MUI v6) library.  
  * **Authentication & Session Management (SPA-friendly, HttpOnly Cookies):**  
    * Develop a React Context (`AuthContext`) and a custom hook (`useAuth`) to manage authentication state and provide authentication functionalities across the application.  
    * Implement the Authorization Code Flow with PKCE client-side: redirecting to the OpenIddict `/connect/authorize` endpoint (hosted by your .NET backend), handling the callback with the authorization code.  
    * Crucially, the backend will handle the exchange of the authorization code for JWTs (containing roles) and then issue `HttpOnly, Secure, SameSite` cookies containing the Access and Refresh Tokens to the React app.  
    * The React app will **NOT** directly read or store JWT Access/Refresh Tokens in JavaScript-accessible storage. All API calls will implicitly rely on the browser sending these HttpOnly token cookies.  
    * Implement custom UI for login (initiating the redirect flow to the backend's authorization endpoint, not Identity's UI), registration (calling the custom backend registration endpoint), logout (clearing local session and calling backend's logout endpoint), and user session management (relying on the backend-issued token cookies).  
    * The React app will not implement explicit token refresh logic as this is handled transparently by the backend; if an API call fails with 401, the backend should attempt to refresh. If that fails (e.g., refresh token expired/invalid), the frontend should be redirected to login.  
  * **Axios Interceptor for API Calls:**  
    * Configure Axios (or a custom fetch wrapper) to use an Interceptor. Its primary role will be to detect 401 Unauthorized responses from the API.  
    * If a 401 occurs, it signifies that the backend-managed token cookies are no longer valid. The interceptor will then redirect the user to the login page to re-initiate the authentication flow.  
    * The interceptor will NOT attach JWTs as they are sent via HttpOnly cookies by the browser.  
  * **Routing & Guards:** Implement React Router for client-side navigation and create private route components to protect authenticated and authorized routes, checking for the presence/validity of the session (e.g., by making a lightweight API call to a `/me` endpoint that relies on the token cookies, and parsing roles from the response to enable client-side role-based rendering).  
  * **Forms:** Utilize React Hook Form for robust login, registration, and user profile forms, integrating with MUI components and using schema validation (e.g., Zod, Yup).  
  * **I18n/L10n:** Integrate `i18next` and `react-i18next` for frontend internationalization, setting up translation files (JSON) and configuring the translation service.  
  * Configure proxy for development server to handle API calls.  
  * **HTTPS Configuration:** Configure the `uphbt-react-frontend` development server to serve the application over **HTTPS**, ensuring all development traffic is encrypted.  
* **Optimal Prompt(s):**  
  * "Guide me through setting up a React v19 project to implement the client-side of the Authorization Code Flow with PKCE for authentication, where the backend issues Access and Refresh Tokens directly into HttpOnly, Secure, SameSite=Lax cookies, and the JWT payload includes user roles. Show the redirect to the backend's authorization endpoint and handling the callback to complete the flow."  
  * "Provide a React `AuthContext` and `useAuth` hook example that manages user login (by redirecting to OpenIddict endpoint), registration (by calling a custom backend API), and logout (by calling a custom backend API to clear token cookies and invalidate tokens), without directly reading JWT Access or Refresh Tokens in JavaScript. Show how to retrieve user roles from a secure backend endpoint (e.g., `/me`) after authentication for client-side authorization logic."  
  * "Generate an Axios Interceptor (or fetch wrapper) in React that detects 401 Unauthorized responses and redirects the user to the login page, since JWTs are managed by the backend via HttpOnly token cookies. Explain why explicit JWT attachment is not needed here."  
  * "How to implement private routes in React Router that check for user authentication and specific roles/permissions (derived from the backend-set token cookie by calling a `/me` endpoint) using the `useAuth` hook."  
  * "How to integrate `i18next` and `react-i18next` into a React v19 project, including setting up translation files and using `useTranslation`."  
  * "Show a React Hook Form example for user login and registration with client-side validation using Zod and MUI components."  
  * "How to configure a Create React App (or similar React setup) development server to serve the application over **HTTPS**, and how to set up proxying for HTTPS backend API calls."

---

## **High-Level Roadmap:**

### **Phase 2: Feature Development & Real-Time Functionality**

**Goal:** Implement the core business logic for Bug and Comment management, including real-time updates.

#### **2.1. Backend: Bug & Comment Management API (uphbt-backend)**

* **Action:**  
  * **Rich Domain Model Implementation:** Implement Bug and Comment as rich domain entities. These entities **will be pure POCOs without any EF Core attributes or specific interfaces**. They will encapsulate their own behavior, containing methods that represent business operations (`Bug.AssignUser()`, `Bug.ChangeStatus()`, `Bug.AddComment()`) and enforce their own invariants.  
  * Define Value Objects (e.g., `BugStatus`, `Severity`, `Priority`) as immutable types where appropriate.  
  * Ensure Bug and Comment entities also include `Guid PublicId` and `byte[] RowVersion` properties for API exposure and optimistic concurrency, respectively. **The mapping of `RowVersion` for optimistic concurrency will be done exclusively in `Uphbt.Data` via Fluent API, respecting the persistence-ignorant nature of the domain model.**  
  * **Repository Pattern Implementation:** Create dedicated EF Core repositories (`IBugRepository`, `ICommentRepository`, `IUserRepository`) in `Uphbt.Data` for data persistence. These repositories will operate on and return your **pure domain entities (POCOs)**. `IUserRepository` will manage `Uphbt.Identity.User` entities.  
  * **Service Layer Implementation:** Develop application services (`BugService`, `CommentService`, `UserService`) in `Uphbt.Services` to orchestrate domain operations, handle transactions, and interact with repositories. Services will be lean, delegating complex logic to the rich domain model. These services will accept and return DTOs defined in `Uphbt.Contracts`. The `UserService` will directly leverage `UserManager<Uphbt.Identity.User>` and `SignInManager<Uphbt.Identity.User>` for user management (registration, profile updates, password changes, role assignments).  
  * **DTOs:** The `Uphbt.Api` layer will use Data Transfer Objects (DTOs) from `Uphbt.Contracts` for API requests and responses, with clear mapping between DTOs and domain entities/service layer results.  
  * **API Endpoints for CRUD:** Create comprehensive RESTful API endpoints for:  
    * Creating, reading (list with filtering/sorting/pagination, by PublicId), updating, and deleting Bugs.  
    * Adding, reading (by Bug PublicId), updating, and deleting Comments.  
    * **Managing User profiles and other non-authentication specific user operations using an injected `IUserService` (from `Uphbt.Services`) that accepts/returns DTOs from `Uphbt.Contracts`.**  
  * **Authorization:** Apply robust authorization attributes (`[Authorize]`, `[Authorize(Roles="Admin")]`, or custom policy-based authorization based on `User` claims/roles derived from the validated JWT in the cookie) to all relevant endpoints to ensure only authorized users can perform actions.  
  * **Real-time Communication with SignalR:**  
    * Implement ASP.NET Core SignalR Hubs (e.g., `BugHub`, `CommentHub`) for real-time updates.  
    * Secure SignalR Hubs using cookie-based authentication (as the frontend will send the HttpOnly token cookies). The primary goal is to ensure only authenticated users with valid sessions can connect and receive updates.  
    * Send real-time notifications to connected clients upon relevant CRUD operations (e.g., "new bug created," "comment added to bug," "bug status changed").  
* **Optimal Prompt(s):**  
  * "Design a **persistence-ignorant** rich domain model for a 'Bug' entity in C\# for a .NET 9 application. Include `long Id`, `Guid PublicId`, `Title`, `Description`, `DateTime CreatedDate`, `string Status`, and `byte[] RowVersion`. Include methods like `ChangeStatus(newStatus)`, `AssignTo(userId)`, `AddComment(comment)`. This class **must be a pure POCO with no EF Core specific attributes like `[Timestamp]`**, respecting the architectural goal of persistence ignorance."  
  * "How to implement an `IBugRepository` interface and its EF Core implementation (`BugRepository`) in .NET 9, ensuring it works with the **persistence-ignorant `Bug` POCO** and includes methods for optimistic concurrency (`RowVersion`) and retrieving entities by `PublicId`?"  
  * "In `Uphbt.Data`, within `ApplicationDbContext`'s `OnModelCreating` method, show me the **Fluent API configuration for the `Bug` entity** to map its `RowVersion` property for optimistic concurrency (`IsRowVersion()`) and any other necessary column mappings. Assume the `Bug` class is a **pure POCO without any attributes**."  
  * "Provide a `BugService` example in .NET 9 that uses the `IBugRepository`, orchestrates operations on the Bug domain entity, and accepts/returns DTOs from `Uphbt.Contracts` while handling DTO to domain entity mapping."  
  * "Generate RESTful API endpoints in a .NET 9 Web API for CRUD operations on 'Bug' entities (using their `PublicId`s) that accept and return DTOs from `Uphbt.Contracts`. Inject an `IBugService` (from `Uphbt.Services`) to handle the business logic. Include `[Authorize]` attributes (e.g., allowing only authenticated users to create, and 'Admin' role to delete), and demonstrate handling optimistic concurrency conflicts with `RowVersion`."  
  * "Guide me through securing an ASP.NET Core SignalR Hub using cookie-based authentication in .NET 9, relying on the HttpOnly token cookies for authentication, and ensuring users are authenticated before connecting."  
  * "Show how to broadcast real-time updates from a SignalR Hub (`BugHub`) to connected clients (e.g., when a bug's status changes or a new comment is added) from a service layer."  
  * "How to implement a `UserService` in `Uphbt.Services` that uses `UserManager<Uphbt.Identity.User>` and `SignInManager<Uphbt.Identity.User>` for user registration, profile updates, and role assignments, accepting and returning DTOs from `Uphbt.Contracts`, thereby abstracting the `Uphbt.Identity.User` entity from the API layer."

---

## **High-Level Roadmap:**

### **Phase 3: Enhancements, Testing & Deployment**

**Goal:** Refine the application with additional features, ensure quality through comprehensive testing, and prepare for deployment.

#### **3.1. Backend: Enhancements & Best Practices (uphbt-backend)**

* **Action:**  
  * **Logging & Monitoring with Serilog:** Integrate Serilog for structured logging across the application. Configure Serilog to write to console, file, and potentially a centralized logging sink (e.g., Seq, Elasticsearch). Implement dependency injection for `ILogger` throughout services and controllers, ensuring logging includes contextual information like authenticated user ID (derived from the validated JWT in the cookie).  
  * **Centralized Exception Handling:** Implement a global exception handling middleware to catch unhandled exceptions, log them with Serilog, and return consistent, standardized `ProblemDetails` (RFC 7807\) responses to the client.  
  * **Robust Input Validation:** Implement comprehensive input validation for all incoming API requests using FluentValidation (recommended) or Data Annotations. Ensure validation failures return standardized `ProblemDetails` responses with clear error details.  
  * **Performance Optimizations:** Utilize asynchronous programming (`async/await`) consistently throughout the call stack. Implement basic caching strategies (e.g., in-memory caching for frequently accessed, less volatile data like static lookup lists or user profiles) where appropriate.  
  * **Advanced Authorization (Policy-Based Authorization):** Implement custom policy-based authorization handlers (e.g., a policy like "CanEditBug" that checks if the current user is the bug reporter, an assigned user, or an 'Admin'). Apply these policies using `[Authorize(Policy="MyCustomPolicy")]` on controllers or actions.  
  * **I18n/L10n:** Refine server-side internationalization, ensuring error messages, validation messages, and API responses can be localized.  
* **Optimal Prompt(s):**  
  * "How to integrate Serilog into a .NET 9 Web API for structured logging, including logging contextual information like the authenticated user's `PublicId` (derived from the validated JWT in the cookie) and using different log sinks?"  
  * "Provide a C\# example of a global exception handling middleware that catches exceptions and maps them to RFC 7807 `ProblemDetails` responses in a .NET 9 Web API."  
  * "Show me how to use FluentValidation to define and apply validation rules for a DTO in `Uphbt.Contracts` and use them for a D.NET 9 Web API, ensuring validation failures are returned as `ProblemDetails`."  
  * "Guide me through creating a custom authorization policy in ASP.NET Core (.NET 9\) that verifies if the authenticated user (identified by `PublicId` from the validated JWT in the cookie) has permission to edit a specific bug (e.g., if they are the reporter or an client)."

#### **3.2. Frontend: Enhancements (Angular & React)**

* **Action:**  
  * **Advanced UI/UX & User Feedback:** Implement more sophisticated UI/UX elements, including global loading indicators, toast notifications for success/error messages (e.g., using Angular Material `SnackBar` or MUI `Snackbar`), and more detailed form feedback.  
  * Implement advanced filtering, sorting, and pagination for data grids (e.g., on the bug list), handling state changes efficiently.  
  * **State Management Refinement:**  
    * **(Angular):** If the application state becomes complex, consider dedicated state management libraries like NgRx (for reactive state management) or Akita (for a more opinionated, simpler approach), understanding their patterns and trade-offs.  
    * **(React):** If global state management needs escalate beyond Context API/hooks, explore Zustand (lightweight and flexible) or Redux Toolkit (opinionated and powerful) for managing application-wide state.  
  * **Client-Side Routing Guards (Refinement):** Ensure routing guards comprehensively handle not only authenticated vs. unauthenticated states but also authorization based on user roles/claims (e.g., `CanActivate` based on user's `Role` or specific permissions retrieved from the backend's `/me` endpoint), integrating with the `AuthService` or `useAuth` hook.  
  * **I18n/L10n:** Implement advanced internationalization features (e.g., language switching, pluralization, date/time formatting) in both Angular (`ngx-translate`) and React (`i18next`).  
* **Optimal Prompt(s):**  
  * "How to implement a global HTTP interceptor in Angular that shows a loading spinner during API calls and displays toast notifications for success/error messages using Angular Material `SnackBar`."  
  * "Show me how to create a custom hook in React using Zustand to manage global application state (e.g., user profile, application settings)."  
  * "Provide an Angular Router Guard example that uses the `AuthService` to check if a user has a specific role (retrieved from the backend `/me` endpoint) before allowing access to a route."  
  * "How to implement a `PrivateRoute` component in React Router that checks for user authentication and specific roles/permissions (retrieved from the backend `/me` endpoint) using the `useAuth` hook."  
  * "Guide me on how to implement language switching and dynamic content translation using `ngx-translate` in Angular, including lazy-loading translation files."

#### **3.3. Testing**

* **Action:**  
  * **Backend Unit & Integration Testing (.NET):**  
    * Write comprehensive unit tests for Rich Domain Entities (ensuring invariants), Value Objects, Services, and Repositories using xUnit and Moq.  
    * Implement integration tests for API controllers using `Microsoft.AspNetCore.Mvc.Testing` (`WebApplicationFactory`), verifying endpoint behavior, data persistence, and especially the security flows with OpenIddict and cookie-based JWTs:  
      * Successful user registration, login, and HttpOnly token cookie issuance (where the JWT includes role claims).  
      * Accessing protected resources with valid and invalid/expired token cookies.  
      * Verification of OpenIddict's Refresh Token Rotation and acquisition of new JWTs in new cookies transparently.  
      * Crucially, test the detection of refresh token reuse (simulating a stolen token attempt on the backend) and verification of proper full session invalidation (all tokens in the chain revoked, cookies cleared).  
    * Test authorization policies (role-based, custom policies) based on role claims in the JWT.  
    * **Verify Persistence Ignorance:** Ensure that unit tests for domain entities do not depend on EF Core or data access concerns, reinforcing their POCO nature.  
* **Optimal Prompt(s):**  
  * "How to write unit tests for a C\# **persistence-ignorant** rich domain entity (e.g., Bug POCO) using xUnit and Moq, focusing solely on testing its business methods and invariants without any EF Core dependencies?"  
  * "Guide me through setting up integration tests for a .NET 9 Web API controller using `WebApplicationFactory`, including testing API endpoint functionality and data persistence with an in-memory database or test database."  
  * "Show me how to write integration tests for a .NET 9 Web API that verify the OpenIddict authentication flow with HttpOnly token cookies, including login (token cookie issuance where the JWT includes role claims), access to protected resources with a valid token cookie, and handling invalid/expired token cookies."  
  * "How to write integration tests to verify OpenIddict's Refresh Token Rotation and Reuse Detection with HttpOnly token cookies in a .NET 9 Web API, simulating token theft and ensuring proper session invalidation?"  
  * "Provide an example of writing unit tests for a .NET service layer (e.g., `BugService`) that interacts with mocked repositories and ensures business logic is correctly applied."

#### **3.4. Deployment (Local/Shareable)**

* **Action:**  
  * **Dockerization:** Create Dockerfiles for the .NET Backend API.  
  * **Docker Compose:** Create a Docker Compose file to orchestrate the backend, database (e.g., SQL Server or PostgreSQL container), and potentially Redis (for distributed cache/session storage if used with BFF pattern).  
  * **Containerization for Frontends:** (Optional, but recommended for consistent environments) Create Dockerfiles for the Angular and React applications, if they will be served via separate containers or a reverse proxy.  
  * **Local Deployment:** Ensure the entire stack can be brought up and down easily using Docker Compose for local development and testing.  
  * **HTTPS Configuration in Docker Compose:** Configure all services (`uphbt-backend`, `uphbt-angular-frontend`, `uphbt-react-frontend`) within the Docker Compose setup to expose and use **HTTPS** for inter-service communication and external access.  
  * **Deployment to Cloud (Future):** Plan for deployment to a cloud provider (e.g., Azure App Service, AWS ECS, Kubernetes) using the created Docker images.  
* **Optimal Prompt(s):**  
  * "Provide a Dockerfile for a .NET 9 Web API that optimizes for multi-stage builds and smaller image size."  
  * "Generate a Docker Compose file that orchestrates a .NET 9 Web API, a SQL Server database, and a Redis instance (for distributed cache), ensuring they can communicate within the Docker network."  
  * "How to containerize an Angular v20 application using Docker for local development and eventual deployment."  
  * "Show me how to containerize a React v19 application using Docker for local development and eventual deployment."  
  * "Guide me on how to configure the .NET 9 Web API and Angular/React applications to correctly communicate when deployed via Docker Compose, especially considering the HttpOnly token cookies."  
  * "How to configure Nginx (or another reverse proxy) within a Docker Compose setup to handle **HTTPS termination** and route traffic to backend and frontend services, including managing certificates (e.g., self-signed for local, or Let's Encrypt for production)."

---

## **Clarification on "Presentation Layer" and REST APIs**

In the context of the .NET Web API and its interaction with Angular/React SPAs, the `Uphbt.Api` project is indeed functioning as the **Presentation Layer** for the backend system.

Here's why and what it implies:

* **Presentation Layer Role:** The `Uphbt.Api` project's primary responsibility is to expose the backend's capabilities to external clients (our Angular and React SPAs) in a consumable format. It defines the public interface of your backend.  
* **RESTful Endpoints as the Interface:** In a RESTful API, the endpoints (e.g., `/api/bugs`, `/api/users/login`) serve as this public interface. They define the resources, the allowed actions (GET, POST, PUT, DELETE), and the data formats (JSON in our case).  
* **Receiving and Sending Data:**  
  * **Incoming Data:** API controllers in `Uphbt.Api` receive HTTP requests from the frontend. These requests contain data (e.g., `BugCreateDto`, `UserLoginDto`) that needs to be processed.  
  * **Outgoing Data:** After processing the request (which involves delegating to the `Uphbt.Services` layer), the controllers format the results back into DTOs (`BugDetailsDto`, `UserTokenDto` \- though we're using cookies for tokens here, DTOs would still be for user profile data, etc.) and send them back as HTTP responses to the frontend.  
* **Mapping Responsibility:** A key function of the `Uphbt.Api` layer is **mapping**. It translates incoming DTOs from the HTTP request into the appropriate parameters for service layer calls, and then translates the results from the service layer (e.g., domain entities or service-specific result objects) back into outgoing DTOs for the HTTP response.  
* **No Business Logic:** Critically, this "Presentation Layer" (the `Uphbt.Api` controllers) should contain **minimal to no business logic**. Its role is strictly to handle HTTP concerns: request parsing, response formatting, authentication/authorization checks (via attributes), and delegating to the application (service) layer. The actual business rules and operations reside in `Uphbt.Services` and `Uphbt.Domain`.

So, stating that `Uphbt.Api` is the "Backend's Presentation Layer, exposing the backend's capabilities via RESTful endpoints" accurately describes its architectural role in this context.
