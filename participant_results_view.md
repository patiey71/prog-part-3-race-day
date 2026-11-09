@model List<RaceDay.Web.Models.RaceResultModel>
@{
    ViewData["Title"] = "My Finish Results";
}

<h2 class="fw-bold mb-4">My Personal Race History</h2>

<div class="row row-cols-1 row-cols-md-2 g-4">
    @if (!Model.Any())
    {
        <div class="col-12"><p class="text-muted">No completed event results available yet.</p></div>
    }
    else
    {
        @foreach (var res in Model)
        {
            <div class="col">
                <div class="card border-0 shadow-sm">
                    <div class="card-body">
                        <h5 class="card-title fw-bold">@res.EventName</h5>
                        <p class="text-muted mb-2">Category: @res.CategoryName</p>
                        <div class="d-flex justify-content-between align-items-center mt-3">
                            <div>
                                <small class="text-uppercase text-muted">Recorded Finish Time</small>
                                <h4 class="fw-bold text-dark mb-0">@res.FinishTime.ToString(@"hh\:mm\:ss")</h4>
                            </div>
                            <div class="text-end">
                                <small class="text-uppercase text-muted">Finished Position</small>
                                <h4 class="fw-bold text-warning mb-0">#@res.FinalPosition</h4>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        }
    }
</div>