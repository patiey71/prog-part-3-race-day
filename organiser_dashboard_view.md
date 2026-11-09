@model List<RaceDay.Web.Models.RaceEventModel>
@{
    ViewData["Title"] = "Organiser Control Panel";
}

<div class="d-flex justify-content-between align-items-center mb-4">
    <h2 class="fw-bold">Organiser Event Dashboard</h2>
    <a asp-action="CreateEvent" class="btn btn-raceday">+ Create New Race Event</a>
</div>

<div class="table-responsive bg-white rounded shadow-sm p-3">
    <table class="table table-hover align-middle">
        <thead class="table-dark">
            <tr>
                <th>Event Name</th>
                <th>Type</th>
                <th>Date</th>
                <th>Venue</th>
                <th>Distance</th>
                <th class="text-end">Management Actions</th>
            </tr>
        </thead>
        <tbody>
            @if (!Model.Any())
            {
                <tr><td colspan="6" class="text-center text-muted">No events listed. Click above to add your first event.</td></tr>
            }
            else
            {
                @foreach (var ev in Model)
                {
                    <tr>
                        <td class="fw-bold">@ev.Name</td>
                        <td><span class="badge bg-secondary">@ev.EventType</span></td>
                        <td>@ev.Date.ToString("yyyy-MM-dd")</td>
                        <td>@ev.Location</td>
                        <td>@ev.DistanceKm km</td>
                        <td class="text-end">
                            <a asp-action="Enrolments" asp-route-id="@ev.Id" class="btn btn-sm btn-outline-info">Enrolments & Results</a>
                            <form asp-action="DeleteEvent" asp-route-id="@ev.Id" method="post" class="d-inline">
                                <button type="submit" class="btn btn-sm btn-outline-danger" onclick="return confirm('Are you sure you want to remove this event?');">Remove</button>
                            </form>
                        </td>
                    </tr>
                }
            }
        </tbody>
    </table>
</div>