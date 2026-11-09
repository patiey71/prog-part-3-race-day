using System.Net.Http.Headers;

var builder = WebApplication.CreateBuilder(args);

// Register MVC controllers with runtime compilation enabled for fast view editing
builder.Services.AddControllersWithViews()
    .AddRazorRuntimeCompilation();

builder.Services.AddHttpContextAccessor();
builder.Services.AddTransient<SessionCookieHandler>();

// Configure 30-minute session timeout for authenticated users
builder.Services.AddSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(30);
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
    options.Cookie.Name = "RaceDay.ClientSession";
});

// Setup primary HTTP Client to communicate with the Part 2 API backend
builder.Services.AddHttpClient("RaceDayApiClient", client =>
{
    var apiEndpoint = builder.Configuration["RaceDayApiConfig:BaseUrl"] ?? "http://localhost:5080/api/";
    client.BaseAddress = new Uri(apiEndpoint);
    client.DefaultRequestHeaders.Accept.Clear();
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
})
.AddHttpMessageHandler<SessionCookieHandler>();

var app = builder.Build();

if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Home/Error");
    app.UseHsts();
}

app.UseHttpsRedirection();
app.UseStaticFiles();

app.UseRouting();
app.UseSession();
app.UseAuthorization();

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

app.Run();

// Custom delegating handler to relay the authentication cookie from MVC to the API backend
public class SessionCookieHandler : DelegatingHandler
{
    private readonly IHttpContextAccessor _contextAccessor;

    public SessionCookieHandler(IHttpContextAccessor contextAccessor)
    {
        _contextAccessor = contextAccessor;
    }

    protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        var currentContext = _contextAccessor.HttpContext;
        if (currentContext != null)
        {
            var sessionCookie = currentContext.Request.Cookies["RaceDay.Session"];
            if (!string.IsNullOrEmpty(sessionCookie))
            {
                request.Headers.Add("Cookie", $"RaceDay.Session={sessionCookie}");
            }
        }
        return await base.SendAsync(request, cancellationToken);
    }
}