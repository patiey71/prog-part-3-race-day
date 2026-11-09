using System.Net.Http.Json;
using Microsoft.AspNetCore.Mvc;
using RaceDay.Web.Models;

namespace RaceDay.Web.Controllers;

public class ParticipantController : Controller
{
    private readonly IHttpClientFactory _clientFactory;

    public ParticipantController(IHttpClientFactory clientFactory)
    {
        _clientFactory = clientFactory;
    }

    private bool CheckParticipantRole() => HttpContext.Session.GetString("UserRole") == "Participant";

    [HttpPost]
    public async Task<IActionResult> Enrol(int eventId, int categoryId)
    {
        if (!CheckParticipantRole()) return RedirectToAction("Login", "Account");

        var api = _clientFactory.CreateClient("RaceDayApiClient");
        var response = await api.PostAsJsonAsync($"events/{eventId}/enrolments", new { categoryId });

        if (response.IsSuccessStatusCode)
        {
            TempData["SuccessMessage"] = "You have successfully registered for this event!";
            return RedirectToAction("MyEnrolments");
        }

        TempData["ErrorMessage"] = "Registration failed. You might already be signed up.";
        return RedirectToAction("Details", "Home", new { id = eventId });
    }

    public async Task<IActionResult> MyEnrolments()
    {
        if (!CheckParticipantRole()) return RedirectToAction("Login", "Account");

        var api = _clientFactory.CreateClient("RaceDayApiClient");
        var response = await api.GetAsync("enrolments/mine");

        List<ParticipantEnrolmentModel> userEnrolments = new();
        if (response.IsSuccessStatusCode)
        {
            userEnrolments = await response.Content.ReadFromJsonAsync<List<ParticipantEnrolmentModel>>() ?? new();
        }

        return View(userEnrolments);
    }

    public async Task<IActionResult> MyResults()
    {
        if (!CheckParticipantRole()) return RedirectToAction("Login", "Account");

        var api = _clientFactory.CreateClient("RaceDayApiClient");
        var response = await api.GetAsync("results/mine");

        List<RaceResultModel> userResults = new();
        if (response.IsSuccessStatusCode)
        {
            userResults = await response.Content.ReadFromJsonAsync<List<RaceResultModel>>() ?? new();
        }

        return View(userResults);
    }
}