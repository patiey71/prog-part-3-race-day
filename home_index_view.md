@model List<RaceDay.Web.Models.RaceEventModel>
@{
    ViewData["Title"] = "Available Races";
}

<div class="p-4 mb-4 rounded-3 bg-dark text-white shadow">
    <div class="container-fluid py-2">
        <h1 class="display-6 fw-bold text-warning">Upcoming Race Events</h1>
        <p class="col-md-8 fs-6">Browse events, register for your category, and view race completion history.</p>
    </div>
</div>

<form method="get" class="row g-3 mb-4">
    <div class="col-md-6">
        <input type="text" name="searchKeyword" class="form-control" placeholder="Search by name or venue location..." value="@ViewData["SearchKeyword"]" />
    </div>
    <div class="col-md-4">
        <select name="selectedType" class="form-select">
            <option value="">All Categories</option>
            <option value="Run">Run</option>
            <option value="Walk">Walk</option>
            <option value="Cycle">Cycle</option>
        </select>
    </div>
    <div class="col-md-2">
        <button type="submit" class="btn btn-raceday w-100">Search</button>
    </div>
</form>

<div class="row row-cols-1 row-cols-md-3 g-4">
    @if (!Model.Any())
    {
        <div class="col-12"><p class="text-muted">No scheduled events found.</p></div>
    }
    else
    {
        @foreach (var ev in Model)
        {
            <div class="col">
                <div class="card h-100 shadow-sm border-0">
                    <div class="card-body">
                        <span class="badge bg-secondary mb-2">@ev.EventType</span>
                        <h5 class="card-title fw-bold text-dark">@ev.Name</h5>
                        <p class="card-text text-muted small">@ev.Description</p>
                        <ul class="list-unstyled text-small mb-3">
                            <li><strong>📅 Date:</strong> @ev.Date.ToString("yyyy-MM-dd HH:mm")</li>
                            <li><strong>📍 Venue:</strong> @ev.Location</li>
                            <li><strong>📏 Distance:</strong> @ev.DistanceKm km</li>
                        </ul>
                    </div>
                    <div class="card-footer bg-white border-0 text-end">
                        <a asp-action="Details" asp-route-id="@ev.Id" class="btn btn-outline-dark btn-sm">View Details & Enrol</a>
                    </div>
                </div>
            </div>
        }
    }
</div>