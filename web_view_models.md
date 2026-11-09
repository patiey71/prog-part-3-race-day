using System.ComponentModel.DataAnnotations;

namespace RaceDay.Web.Models;

public class UserLoginModel
{
    [Required(ErrorMessage = "Please enter your email address")]
    [EmailAddress]
    public string AccountEmail { get; set; } = string.Empty;

    [Required(ErrorMessage = "Password is required")]
    [DataType(DataType.Password)]
    public string AccountPassword { get; set; } = string.Empty;
}

public class UserRegistrationModel
{
    [Required, Display(Name = "First Name")]
    public string UserFirstName { get; set; } = string.Empty;

    [Required, Display(Name = "Last Name")]
    public string UserLastName { get; set; } = string.Empty;

    [Required, EmailAddress, Display(Name = "Email Address")]
    public string UserEmail { get; set; } = string.Empty;

    [Required, DataType(DataType.Password), StringLength(100, MinimumLength = 8)]
    public string UserPassword { get; set; } = string.Empty;

    [Required, Display(Name = "User Role")]
    public string AssignedRole { get; set; } = "Participant"; // Organiser or Participant

    [Display(Name = "Date of Birth")]
    public DateTime? BirthDate { get; set; }

    [Display(Name = "Contact Number")]
    public string? ContactNumber { get; set; }

    [Display(Name = "Organiser Access Passcode")]
    public string? OrganiserCode { get; set; }
}

public class RaceEventModel
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public string Description { get; set; } = string.Empty;
    public DateTime Date { get; set; }
    public string Location { get; set; } = string.Empty;
    public decimal DistanceKm { get; set; }
    public string EventType { get; set; } = string.Empty;
    public int OrganiserId { get; set; }
    public string OrganiserName { get; set; } = string.Empty;
}

public class CreateRaceEventModel
{
    public int? Id { get; set; }

    [Required, StringLength(200)]
    public string EventName { get; set; } = string.Empty;

    [Required, StringLength(2000)]
    public string DetailedDescription { get; set; } = string.Empty;

    [Required, Display(Name = "Scheduled Date")]
    public DateTime? EventDate { get; set; }

    [Required, StringLength(300)]
    public string VenueLocation { get; set; } = string.Empty;

    [Range(0.01, 1000), Display(Name = "Race Distance (Km)")]
    public decimal DistanceInKm { get; set; }

    [Required, Display(Name = "Activity Type")]
    public string ActivityType { get; set; } = "Run";
}

public class RaceCategoryModel
{
    public int Id { get; set; }
    public int EventId { get; set; }
    public string CategoryTitle { get; set; } = string.Empty;
}

public class ParticipantEnrolmentModel
{
    public int Id { get; set; }
    public int EventId { get; set; }
    public string EventName { get; set; } = string.Empty;
    public int ParticipantId { get; set; }
    public string ParticipantName { get; set; } = string.Empty;
    public int CategoryId { get; set; }
    public string CategoryName { get; set; } = string.Empty;
    public DateTime EnrolledAt { get; set; }
    public string RegistrationStatus { get; set; } = "Confirmed";
}

public class RaceResultModel
{
    public int Id { get; set; }
    public int EnrolmentId { get; set; }
    public int EventId { get; set; }
    public string EventName { get; set; } = string.Empty;
    public int ParticipantId { get; set; }
    public string ParticipantName { get; set; } = string.Empty;
    public string CategoryName { get; set; } = string.Empty;
    public TimeSpan FinishTime { get; set; }
    public int FinalPosition { get; set; }
}

public class ResultCaptureModel
{
    [Required]
    public int EventId { get; set; }

    [Required]
    public int EnrolmentId { get; set; }

    [Required, Display(Name = "Time Taken (hh:mm:ss)")]
    public TimeSpan RecordedTime { get; set; }

    [Range(1, 100000), Display(Name = "Overall Position")]
    public int FinishPosition { get; set; }
}