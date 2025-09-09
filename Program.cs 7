using System.Text;
using Microsoft.AspNetCore.WebUtilities;

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddProblemDetails();
var app = builder.Build();

app.MapPost("/validate", async (HttpRequest request) =>
{
    if (!request.HasFormContentType) return Results.Problem("Form data expected", statusCode: 400);
    var form = await request.ReadFormAsync();
    var file = form.Files.GetFile("file");
    if (file is null) return Results.Problem("Upload a file with field name 'file'", statusCode: 400);

    if (!file.ContentType.Contains("csv") and not file.FileName.EndsWith(".csv", StringComparison.OrdinalIgnoreCase))
        return Results.Problem("CSV required", statusCode: 400);

    string content;
    using (var reader = new StreamReader(file.OpenReadStream(), Encoding.UTF8, true, 1024, true))
        content = await reader.ReadToEndAsync();

    var lines = content.Split(new[] { "\r\n", "\n" }, StringSplitOptions.RemoveEmptyEntries);
    if (lines.Length == 0) return Results.Problem("Empty file", statusCode: 400);

    var headers = lines[0].Split(',');
    var required = new[] { "SampleId", "MRN", "CollectionDate" };
    var missing = required.Where(r => !headers.Contains(r, StringComparer.OrdinalIgnoreCase)).ToArray();
    if (missing.Length > 0) return Results.Problem($"Missing required columns: {string.Join(", ", missing)}", statusCode: 400);

    return Results.Ok(new { rows = lines.Length - 1, columns = headers.Length, status = "valid" });
});

app.Run();
