# CsvUploadValidator
Minimal ASP.NET Core endpoint to validate an uploaded CSV has required headers.

## Run
```bash
dotnet run
# validate
curl -F "file=@your.csv" http://localhost:5000/validate
```
Customize `required` headers in `Program.cs`. Add auth, size limits, and streaming for large files.
