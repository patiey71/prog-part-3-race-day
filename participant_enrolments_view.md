@model List<RaceDay.Web.Models.ParticipantEnrolmentModel>
@{
    ViewData["Title"] = "My Registered Events";
}

<h2 class="fw-bold mb-4">My Race Registrations</h2>

<div class="table-responsive bg-white rounded shadow-sm p-3">
    <table class="table table-hover align-middle">
        <thead class="table-dark">
            <tr>
                <th>Event Title</th>
                <th>Category</th>
                <th>Enrolled Date</th>
                <th>Status</th>
            </tr>
        </thead>
        <tbody>
            @if (!Model.Any())
            {
                <tr><td colspan="4" class="text-center text-muted">You are not enrolled in any events yet.</td></tr>
            }
            else
            {
                @foreach (var item in Model)
                {
                    <tr>
                        <td class="fw-bold">@item.EventName</td>
                        <td>@item.CategoryName</td>
                        <td>@item.EnrolledAt.ToString("yyyy-MM-dd")</td>
                        <td>
                            <span class="badge @(item.RegistrationStatus == "Confirmed" ? "bg-success" : "bg-warning text-dark")">
                                @item.RegistrationStatus
                            </span>
                        </td>
                    </tr>
                }
            }
        </tbody>
    </table>
</div>