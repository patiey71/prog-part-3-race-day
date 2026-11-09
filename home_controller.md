using System.Net.Http.Json;
using Microsoft.AspNetCore.Mvc;
using RaceDay.Web.Models;

namespace RaceDay.Web.Controllers;

public class HomeController : Controller
{
    private readonly IHttpClientFactory _clientFactory;

    public HomeController(IHttpClientFactory clientFactory)
    {
        _clientFactory = clientFactory;
    }

    public async Task<IActionResult> Index(string? searchKeyword, string? selectedType)
    {
        var api = _clientFactory.CreateClient("RaceDayApiClient");
        var response = await api.GetAsync("events");

        List<RaceEventModel> raceList = new();
        if (response.IsSuccessStatusCode)
        {
            raceList = await response.Content.ReadFromJsonAsync<List<RaceEventModel>>() ?? new();
        }

        // Apply dynamic filtering if user entered search criteria
        if (!string.IsNullOrWhiteSpace(searchKeyword))
        {
            raceList = raceList.Where(e => e.Name.Contains(searchKeyword, StringComparison.OrdinalIgnoreCase) ||
                                           e.Location.Contains(searchKeyword, StringComparison.OrdinalIgnoreCase)).ToList();
        }

        if (!string.IsNullOrWhiteSpace(selectedType))
        {
            raceList = raceList.Where(e => e.EventType.Equals(selectedType, StringComparison.OrdinalIgnoreCase)).ToList();
        }

        ViewData["SearchKeyword"] = searchKeyword;
        ViewData["SelectedType"] = selectedType;
        return View(raceList);
    }

    public async Task<IActionResult> Details(int id)
    {
        var api = _clientFactory.CreateClient("RaceDayApiClient");
        var eventResp = await api.GetAsync($"events/{id}");

        if (!eventResp.IsSuccessStatusCode) return NotFound();

        var selectedEvent = await eventResp.Content.ReadFromJsonAsync<RaceEventModel>();
        var categoryResp = await api.GetAsync($"events/{id}/categories");

        List<RaceCategoryModel> eventCategories = new();
        if (categoryResp.IsSuccessStatusCode)
        {
            eventCategories = await categoryResp.Content.ReadFromJsonAsync<List<RaceCategoryModel>>() ?? new();
        }

        ViewBag.Categories = eventCategories;
        return View(selectedEvent);
    }
}