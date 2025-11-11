# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is the Dell OpenManage Enterprise (OME/OME-M) API examples repository. It contains standalone, self-contained scripts demonstrating how to use the OME REST API. Scripts are maintained in both Python and PowerShell with functional parity between implementations.

**Repository Status**: Maintenance mode - still functional and monitored, but commits may be infrequent.

## Code Architecture

### Self-Contained Script Design

**Critical**: Every script is intentionally self-contained and standalone. The repository deliberately does NOT use an internal library or shared modules. Each script includes its own copies of common functions (authentication, API interaction, device resolution, etc.).

This design allows users to:
- Copy any single script and use it independently
- Modify scripts without breaking dependencies
- Understand complete workflows within a single file

### Directory Structure

- `Python/` - Python 3 implementation of API examples (~49 scripts)
- `PowerShell/` - PowerShell 7 implementation (~35 scripts)
- `docs/` - Documentation including API reference, contributing guidelines, and library code patterns
  - `docs/python_library_code.md` - Standard functions for common Python tasks
  - `docs/powershell_library_code.md` - Standard functions for common PowerShell tasks
  - `docs/API.md` - Complete script listing organized by functionality
  - `docs/CONTRIBUTING.md` - Detailed coding standards

### Script Categories

Scripts are organized by function type:
- **Deploy**: Discovery, grouping, template creation, network configuration
- **Update**: Firmware updates, inventory refresh
- **Monitor**: Alerts, audit logs, device inventory, reports
- **Maintenance**: Lead retirement, VLAN profiles
- **Plugin-specific**: Power Manager, SupportAssist Enterprise, OpenManage Integration plugins

### Naming Conventions

- PowerShell: `Verb-ThePurpose.ps1` (e.g., `Get-DeviceList.ps1`) using [approved PowerShell verbs](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/approved-verbs-for-windows-powershell-commands)
- Python: `verb_the_purpose.py` (e.g., `get_device_list.py`)
- Equivalent scripts in both languages should have matching names

## Development Commands

### Python Development

**No package manager or test framework**: Scripts are standalone with no centralized requirements file or test suite.

To run a Python script:
```bash
python <script_name>.py --ip <ome_ip> --user <username> --password <password>
```

Required libraries are handled per-script with try/except blocks for imports. Common requirements:
- `urllib3` - For HTTPS with unverified certificates
- `requests` - For REST API calls

### PowerShell Development

**Requires PowerShell 7**: All new scripts require PowerShell 7+. Check for `#Requires -Version 7` at the top of scripts.

To run a PowerShell script:
```powershell
$cred = Get-Credential
.\Script-Name.ps1 -IpAddress "10.xx.xx.xx" -Credentials $cred
```

Use `Format Document` in VS Code to auto-format PowerShell code to best practices.

## Key Patterns and Standards

### Authentication Pattern

Both languages use X-Auth token authentication (not Basic Auth):
- Python: `authenticate()` function returns headers dict with X-Auth-Token
- PowerShell: Uses `-Credential` parameter (pscredential type)

### API Resource Interaction

**Always use OData filters** instead of exhaustive dictionary searches for scalability:

Bad:
```python
device_list = get_data(authenticated_headers, f"https://{ome_ip}/api/DeviceService/Devices")
for device in device_list:
    if device['DeviceName'] == device_name:
        device_id = device['Id']
```

Good:
```python
get_data(authenticated_headers, f"https://{ome_ip}/api/DeviceService/Devices",
         f"DeviceName eq '{device_name}'")
```

Common OData filters:
- Device by name: `DeviceName eq '<name>'`
- Device by service tag: `DeviceServiceTag eq '<tag>'`
- Group by name: `Name eq '<group_name>'`

### Standard Functions (Copy from Library Code)

When writing new scripts, copy these standard functions from `docs/python_library_code.md` or `docs/powershell_library_code.md`:
- **authenticate()** / **New-OMESession** - Session creation
- **get_data()** / **Get-Data** - GET requests with pagination and OData filters
- **get_device_id()** - Resolve device name/service tag to ID
- **get_group_id()** - Resolve group name to ID
- **track_job_to_completion()** / **Wait-OnJob** - Poll job status until completion

## Python Coding Standards

- **Python 3 only**
- **PEP8 compliant**: Use pylint for validation
- **PEP484 type hints**: All function parameters and return types must be annotated
  ```python
  def authenticate(ome_ip_address: str, ome_username: str, ome_password: str) -> dict:
  ```
- **Docstrings**: Use Google-style docstrings with Args, Returns, Raises sections
- **Module imports**: Wrap non-standard library imports in try/except with installation instructions
- **Naming**: `snake_case` for variables/functions, `PascalCase` for classes, `UPPER_CASE` for constants
- **No exit() in functions**: Raise exceptions instead; handle in main()
- **Password handling**: Use `getpass()` module for optional password prompts
- **Script header**: Use markdown format with Synopsis, Description, Python Example sections

## PowerShell Coding Standards

- **PowerShell 7 required**: Include `#Requires -Version 7` at top
- **Output**: Use `Write-Host` for status messages (preserves piping)
- **Function naming**: Use approved PowerShell verbs
- **Variable naming**: `CamelCase` for variables, `ALLCAPS` for constants
- **REST calls**: Use `Invoke-RestMethod` with `-SkipCertificateCheck`
- **Credentials**: Use `[pscredential]` type, pass with `-Credential` parameter
- **Parameter checking**: Use `$PSBoundParameters.ContainsKey('<PARAM_NAME>')`
- **Timing loops**: Use `[System.Diagnostics.Stopwatch]` instead of counters
- **JSON creation**: Use PowerShell hashtables converted with `ConvertTo-Json`, not string templates
- **Function documentation**: Include full comment-based help with .SYNOPSIS, .DESCRIPTION, .PARAMETER, .OUTPUTS

## API Resource Reference

OME REST API base URL: `https://<ome_ip>/api/`

Key endpoints:
- `/SessionService/Sessions` - Authentication
- `/DeviceService/Devices` - Device management
- `/GroupService/Groups` - Group management
- `/TemplateService/Templates` - Template operations
- `/JobService/Jobs` - Job tracking

API Documentation: https://dl.dell.com/topicspdf/dell-openmanage-enterprise_Reference-Guide2_en-us.pdf

## Working with This Repository

### When modifying existing scripts:
1. Maintain self-contained nature - don't extract shared libraries
2. Preserve both Python and PowerShell equivalents if they exist
3. Follow the exact coding standards in `docs/CONTRIBUTING.md`
4. Use OData filters for device/group lookups
5. Copy standard functions from library code docs

### When creating new scripts:
1. Reference `docs/CONTRIBUTING.md` for complete standards
2. Copy authentication and get_data functions from library code
3. Add comprehensive documentation header
4. Create equivalent script in other language if possible
5. Update `docs/API.md` with script documentation

### For git commits:
- Use format: `file_name: Description` for single-file changes
- Include `Signed-off-by: FirstName LastName <email>` at end
- Reference issues with `#<ticket_number>` in commit message
- Rebase cleanly on main branch before PR
