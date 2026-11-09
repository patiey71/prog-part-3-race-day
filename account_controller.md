using System.Net.Http.Json;
using Microsoft.AspNetCore.Mvc;
using RaceDay.Web.Models;

namespace RaceDay.Web.Controllers;

public class AccountController : Controller
{
    private readonly IHttpClientFactory _clientFactory;

    public AccountController(IHttpClientFactory clientFactory)
    {
        _clientFactory = clientFactory;
    }

    [HttpGet]
    public IActionResult Login() => View();

    [HttpPost]
    public async Task<IActionResult> Login(UserLoginModel input)
    {
        if (!ModelState.IsValid) return View(input);

        var api = _clientFactory.CreateClient("RaceDayApiClient");
        
        // Map form fields to API expected DTO properties
        var payload = new { email = input.AccountEmail, password = input.AccountPassword };
        var response = await api.PostAsJsonAsync("auth/login", payload);

        if (response.IsSuccessStatusCode)
        {
            var userProfile = await response.Content.ReadFromJsonAsync<UserProfileDto>();
            if (userProfile != null)
            {
                // Preserve user identity details in browser session
                HttpContext.Session.SetString("UserRole", userProfile.Role);
                HttpContext.Session.SetString("UserName", $"{userProfile.FirstName} {userProfile.LastName}");
                HttpContext.Session.SetString("UserId", userProfile.Id.ToString());
            }

            if (userProfile?.Role == "Organiser")
                return RedirectToAction("Dashboard", "Organiser");

            return RedirectToAction("Index", "Home");
        }

        ModelState.AddModelError(string.Empty, "Authentication failed. Check your login details.");
        return View(input);
    }

    [HttpGet]
    public IActionResult Register() => View();

    [HttpPost]
    public async Task<IActionResult> Register(UserRegistrationModel input)
    {
        if (!ModelState.IsValid) return View(input);

        var api = _clientFactory.CreateClient("RaceDayApiClient");
        var payload = new
        {
            firstName = input.UserFirstName,
            lastName = input.UserLastName,
            email = input.UserEmail,
            password = input.UserPassword,
            role = input.AssignedRole,
            dateOfBirth = input.BirthDate,
            phoneNumber = input.ContactNumber,
            organiserCode = input.OrganiserCode
        };

        var response = await api.PostAsJsonAsync("auth/register", payload);

        if (response.IsSuccessStatusCode)
        {
            TempData["SuccessMessage"] = "Account registered successfully! Please log in.";
            return RedirectToAction("Login");
        }

        ModelState.AddModelError(string.Empty, "Unable to complete registration. Verify details or Organiser passcode.");
        return View(input);
    }

    public async Task<IActionResult> Logout()
    {
        var api = _clientFactory.CreateClient("RaceDayApiClient");
        await api.PostAsync("auth/logout", null);
        HttpContext.Session.Clear();
        return RedirectToAction("Login");
    }

    private record UserProfileDto(int Id, string FirstName, string LastName, string Email, string Role, DateTime? DateOfBirth, string? PhoneNumber);
}