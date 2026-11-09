@model List<RaceDay.Web.Models.ParticipantEnrolmentModel>
@{
    ViewData["Title"] = "Manage Enrolments & Results";
    int eventId = ViewBag.EventId;
}

<h2 class="fw-bold mb-4">Event Enrolments & Finish Times</h2>

<div class="table-responsive bg-white rounded shadow-sm p-3 mb-4">
    <table class="table table-hover align-middle">
        <thead class="table-dark">
            <tr>
                <th>Participant Name</th>
                <th>Assigned Category</th>
                <th>Enrolled Date</th>
                <th>Capture Finish Result</th>
            </tr>
        </thead>
        <tbody>
            @if (!Model.Any())
            {
                <tr><td colspan="4" class="text-center text-muted">No registered participants for this event yet.</td></tr>
            }
            else
            {
                @foreach (var item in Model)
                {
                    <tr>
                        <td class="fw-bold">@item.ParticipantName</td>
                        <td>@item.CategoryName</td>
                        <td>@item.EnrolledAt.ToString("yyyy-MM-dd HH:mm")</td>
                        <td>
                            <form asp-action="CaptureResult" method="post" class="row g-2 align-items-center">
                                <input type="hidden" name="EventId" value="@eventId" />
                                <input type="hidden" name="EnrolmentId" value="@item.Id" />
                                <div class="col-auto">
                                    <input type="text" name="RecordedTime" class="form-control form-control-sm" placeholder="00:45:00" required />
                                </div>
                                <div class="col-auto">
                                    <input type="number" name="FinishPosition" class="form-control form-control-sm" placeholder="Position #" required />
                                </div>
                                <div class="col-auto">
                                    <button type="submit" class="btn btn-sm btn-raceday">Save</button>
                                </div>
                            </form>
                        </td>
                    </tr>
                }
            }
        </tbody>
    </table>
</div>

<a asp-action="Dashboard" class="btn btn-secondary btn-sm">&larr; Back to Dashboard</a>