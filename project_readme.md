# RaceDay Platform (PROG6212 POE – Parts 2 & 3)

The **RaceDay Platform** is an end-to-end ASP.NET Core web system designed for managing sporting events (runs, walks, cycles). It features a RESTful API backend (`RaceDay.Api`) and an ASP.NET Core MVC web front end (`RaceDay.Web`) that consumes the API.

[![CI Pipeline](https://github.com/<your-username>/<your-repo>/actions/workflows/ci.yml/badge.svg)](https://github.com/<your-username>/<your-repo>/actions)

---

## Project Overview & Architecture

The solution strictly enforces separation of concerns:
- **`RaceDay.Api`**: Handles database interactions via Entity Framework Core (SQL Server), session authentication, password hashing (BCrypt), role authorization, and Azure Blob Storage integration.
- **`RaceDay.Web`**: The ASP.NET Core MVC front end. Contains **no direct database access or replicated business logic**; all operations communicate directly with `RaceDay.Api`.
- **`RaceDay.Tests`**: Suite of xUnit integration tests using an in-memory database to verify authentication, event CRUD, role permissions, enrolments, and result capturing.

---

## Features

### Organiser Capabilities
- **Dashboard**: High-level overview of events, total enrolments, and upcoming race dates.
- **Events Management**: Create, view, update, and delete race events (Name, Description, Date, Location, Distance, Activity Type).
- **Banner Image Handling**: Upload event banner images, stored via Azure Blob Storage through the API.
- **Category Management**: Define age and distance categories per event (e.g., "Under 20", "Senior", "10km").
- **Enrolments & Results Entry**: View enrolled participants per category and capture finish times/positions after event completion.

### Participant Capabilities
- **Event Discovery**: Search and filter upcoming race events by activity type or keyword.
- **Instant Enrolment**: Register for events by choosing an available category.
- **My Enrolments**: Personal view of enrolled races and current registration status (`Confirmed` / `Pending`).
- **My Results**: View complete race history across finished events, including recorded finish time and final position (e.g., *47th out of 312 finishers*).
- **Profile Management**: View and update profile information and avatar pictures.

---

##  Getting Started

### Prerequisites
- [.NET 8.0 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- Visual Studio 2022 (with *ASP.NET and web development* workload) or VS Code
- SQL Server / LocalDB
- Docker Desktop (for containerized execution)

---

### Local Setup & Execution

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/<your-username>/<your-repo>.git
   cd <your-repo>
   ```

2. **Database Setup:**
   Ensure your SQL Server LocalDB instance is running, or update the `DefaultConnection` string in `RaceDay.Api/appsettings.json`.
   ```bash
   dotnet ef database update --project RaceDay.Api
   ```
   *(Note: If no database exists, EF Core will automatically initialize the schema on the first API execution.)*

3. **Run the API Project:**
   ```bash
   dotnet run --project RaceDay.Api
   ```
   - API standard URL: `http://localhost:5080`
   - Swagger Documentation UI: `http://localhost:5080/swagger`

4. **Run the MVC Web Application:**
   Open a secondary terminal window and run:
   ```bash
   dotnet run --project RaceDay.Web
   ```
   - MVC Web Portal URL: `http://localhost:5247` (or port specified in `launchSettings.json`)

5. **Run Unit & Integration Tests:**
   ```bash
   dotnet test RaceDay.sln
   ```

---

##  Docker Deployment

Both the Web application and API can be run inside Docker containers.

### Running the MVC App via Docker

1. **Build the Container Image:**
   ```bash
   docker build -t raceday-web -f RaceDay.Web/Dockerfile .
   ```

2. **Run the Container:**
   ```bash
   docker run -d -p 8080:80 --name raceday-app raceday-web
   ```
   Access the containerized web portal at `http://localhost:8080`

---

##  Azure Blob Storage Integration

File uploads (such as event banner images and profile avatars) are integrated into the `RaceDay.Api` layer. 

- The MVC web application forwards file streams to API upload endpoints (`multipart/form-data`).
- The API uses `Azure.Storage.Blobs` SDK to stream images to configured Azure Blob containers.
- Standard URLs pointing to uploaded Azure Blob files are returned and rendered across event detail views.

---

##  CI/CD Pipeline

The repository uses **GitHub Actions** (`.github/workflows/ci.yml`) to automatically validate every push and pull request:
1. Restores NuGet dependencies across `RaceDay.sln`.
2. Compiles both API and MVC projects.
3. Runs all xUnit tests to verify functionality and role permissions.

---

##  Video Demonstration

A complete walk-through demonstrating the running application, MVC architecture, Azure Blob Storage implementation, and Docker deployment is available here:

- **YouTube Video (Unlisted):*

---

##License & Disclaimer
Submitted as part of the **PROG6212 Portfolio of Evidence (POE)** assessment. All rights reserved.
