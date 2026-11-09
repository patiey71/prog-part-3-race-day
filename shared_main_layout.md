<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - RaceDay Web Portal</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" />
    <style>
        :root { --raceday-primary: #0284c7; --raceday-accent: #f97316; }
        .bg-raceday { background-color: var(--raceday-primary) !important; color: white; }
        .btn-raceday { background-color: var(--raceday-accent); color: white; border: none; font-weight: 600; }
        .btn-raceday:hover { background-color: #ea580c; color: white; }
    </style>
</head>
<body class="bg-light d-flex flex-column min-vh-100">
    <header>
        <nav class="navbar navbar-expand-lg navbar-dark bg-dark shadow">
            <div class="container">
                <a class="navbar-brand fw-bold fs-4 text-warning" asp-controller="Home" asp-action="Index">🏃 RaceDay Portal</a>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navContent">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="collapse navbar-collapse" id="navContent">
                    <ul class="navbar-nav me-auto">
                        <li class="nav-item"><a class="nav-link" asp-controller="Home" asp-action="Index">Races</a></li>
                        @if (Context.Session.GetString("UserRole") == "Organiser")
                        {
                            <li class="nav-item"><a class="nav-link text-info fw-semibold" asp-controller="Organiser" asp-action="Dashboard">Organiser Dashboard</a></li>
                        }
                        else if (Context.Session.GetString("UserRole") == "Participant")
                        {
                            <li class="nav-item"><a class="nav-link" asp-controller="Participant" asp-action="MyEnrolments">My Enrolments</a></li>
                            <li class="nav-item"><a class="nav-link" asp-controller="Participant" asp-action="MyResults">My Results</a></li>
                        }
                    </ul>
                    <ul class="navbar-nav">
                        @if (string.IsNullOrEmpty(Context.Session.GetString("UserRole")))
                        {
                            <li class="nav-item me-2"><a class="btn btn-outline-light btn-sm" asp-controller="Account" asp-action="Login">Sign In</a></li>
                            <li class="nav-item"><a class="btn btn-raceday btn-sm" asp-controller="Account" asp-action="Register">Register Account</a></li>
                        }
                        else
                        {
                            <li class="nav-item dropdown">
                                <a class="nav-link dropdown-toggle text-warning" href="#" role="button" data-bs-toggle="dropdown">
                                    👤 @Context.Session.GetString("UserName") (@Context.Session.GetString("UserRole"))
                                </a>
                                <ul class="dropdown-menu dropdown-menu-end">
                                    <li><a class="dropdown-item text-danger" asp-controller="Account" asp-action="Logout">Sign Out</a></li>
                                </ul>
                            </li>
                        }
                    </ul>
                </div>
            </div>
        </nav>
    </header>

    <div class="container my-4 flex-grow-1">
        @if (TempData["SuccessMessage"] != null)
        {
            <div class="alert alert-success alert-dismissible fade show shadow-sm" role="alert">
                @TempData["SuccessMessage"]
                <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
            </div>
        }
        @if (TempData["ErrorMessage"] != null)
        {
            <div class="alert alert-danger alert-dismissible fade show shadow-sm" role="alert">
                @TempData["ErrorMessage"]
                <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
            </div>
        }

        @RenderBody()
    </div>

    <footer class="bg-dark text-white text-center py-3 mt-auto border-top">
        <div class="container">
            <small>&copy; 2026 RaceDay Platform - PROG6212 POE Part 3 Submission</small>
        </div>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>