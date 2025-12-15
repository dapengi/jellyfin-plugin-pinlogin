# Jellyfin JellyPin Plugin

A Jellyfin plugin that enables users to authenticate using a 4-digit PIN for quick access, similar to the PIN login feature available in Jellyfin versions prior to 10.9.

## Features

- **4-Digit PIN Authentication**: Users can log in using a simple 4-digit PIN instead of full passwords
- **Dual Authentication**: PIN for quick access, password still required for administrative functions
- **Admin-Managed PINs**: Administrators set and manage user PINs through a web interface
- **Security Features**:
  - PIN hashing (SHA256) - PINs are never stored in plaintext
  - Rate limiting to prevent brute force attacks
  - Configurable lockout after failed attempts
  - Automatic lockout expiration
- **Universal Client Support**: Works with all Jellyfin clients (web, mobile, TV apps)
- **Easy Configuration**: Simple web-based admin interface for PIN management

## Requirements

- Jellyfin 10.9.0 or later (tested with 10.11.5)
- .NET 8.0 Runtime

## Installation

### Method 1: Install from Repository (Recommended when available)

1. Open Jellyfin Dashboard
2. Go to **Plugins** → **Repositories**
3. Add the plugin repository URL (if you're hosting one)
4. Navigate to **Catalog**
5. Find "JellyPin" and click **Install**
6. Restart Jellyfin

### Method 2: Manual Installation

1. **Build the Plugin**:
   ```bash
   cd Jellyfin.Plugin.PinLogin
   dotnet build --configuration Release
   ```

2. **Locate the Plugin DLL**:
   After building, find the compiled DLL at:
   ```
   bin/Release/net8.0/Jellyfin.Plugin.PinLogin.dll
   ```

3. **Copy to Jellyfin Plugins Directory**:

   Copy the DLL to your Jellyfin plugins folder:

   - **Linux**: `/var/lib/jellyfin/plugins/PinLogin/`
   - **Windows**: `%AppData%\Jellyfin\Server\plugins\PinLogin\`
   - **macOS**: `~/.local/share/jellyfin/plugins/PinLogin/`
   - **Docker**: `/config/plugins/PinLogin/` (or your configured path)

   Create the `PinLogin` folder if it doesn't exist.

4. **Restart Jellyfin**:
   Restart your Jellyfin server to load the plugin.

## Configuration

### Initial Setup

1. Log in to Jellyfin Dashboard as an administrator
2. Navigate to **Dashboard** → **Plugins** → **JellyPin**
3. Configure security settings:
   - **Max Failed Attempts**: Number of failed PIN attempts before lockout (default: 5)
   - **Lockout Duration**: How long to lock out after failed attempts in minutes (default: 15)
   - **Enable Rate Limiting**: Toggle rate limiting on/off (recommended: enabled)

### Setting User PINs

1. In the JellyPin configuration page, scroll to **User PIN Management**
2. You'll see a list of all users with their PIN status
3. Click the **edit** icon next to a user to set their PIN
4. Enter a 4-digit PIN (numbers only)
5. Click **Set PIN**

### Removing User PINs

1. In the User PIN Management section, find the user
2. Click the **delete** icon next to a user with an existing PIN
3. Confirm the removal

## Usage

### For Users

Once a PIN is set by an administrator:

1. **On Login Screen**:
   - Enter your username
   - Enter your 4-digit PIN in the password field
   - Click **Sign In**

2. **Security Notes**:
   - After 5 failed PIN attempts (default), your account will be locked for 15 minutes (default)
   - You'll see how many attempts remain after each failed login
   - Password authentication still works for administrative functions

### For Administrators

- Users can log in with their PIN for general access
- Administrative operations may still require password authentication
- PINs are hashed and stored securely
- You can view which users have PINs configured
- You can change or remove PINs at any time

## Security Considerations

### PIN Hashing
All PINs are hashed using SHA256 before storage. The plugin never stores plaintext PINs.

### Rate Limiting
When enabled, rate limiting provides:
- Track of failed authentication attempts per user
- Automatic lockout after configurable failed attempts
- Time-based lockout expiration
- Prevention of brute force attacks

### Authentication Separation
- PIN authentication is designed for quick, frequent logins
- Password authentication remains available for administrative tasks
- This provides a balance between convenience and security

## Troubleshooting

### Plugin Not Appearing
- Verify the DLL is in the correct plugins folder
- Check Jellyfin logs for plugin loading errors
- Ensure Jellyfin has been restarted after installation

### JellyPin Not Working
- Verify the user has a PIN set in the admin interface
- Check that the PIN is exactly 4 digits
- Verify rate limiting hasn't locked out the user
- Check Jellyfin logs for authentication errors

### Rate Limiting Issues
- If a user is locked out, wait for the lockout duration to expire
- Administrators can adjust lockout settings in plugin configuration
- Check the configured max failed attempts and lockout duration

### Build Issues
- Ensure .NET 8.0 SDK is installed
- Verify Jellyfin package versions in the .csproj file match your Jellyfin version
- Try cleaning the build: `dotnet clean && dotnet build`

## Development

### Building from Source

```bash
# Clone or navigate to the plugin directory
cd Jellyfin.Plugin.PinLogin

# Restore dependencies
dotnet restore

# Build the plugin
dotnet build --configuration Release

# The output will be in bin/Release/net8.0/
```

### Project Structure

```
Jellyfin.Plugin.PinLogin/
├── Api/
│   └── PinController.cs          # REST API endpoints for PIN management
├── Configuration/
│   ├── PluginConfiguration.cs    # Configuration model
│   └── configPage.html            # Admin web interface
├── Security/
│   └── RateLimitService.cs       # Rate limiting and lockout logic
├── Plugin.cs                      # Main plugin class
├── PinAuthenticationProvider.cs  # Authentication provider implementation
└── Jellyfin.Plugin.PinLogin.csproj
```

## API Endpoints

The plugin exposes these REST API endpoints (admin access required):

- `GET /PinLogin/Users` - Get all users and their PIN status
- `POST /PinLogin/Users/{userId}/Pin` - Set a PIN for a user
- `DELETE /PinLogin/Users/{userId}/Pin` - Remove a user's PIN

## Compatibility

- **Jellyfin Version**: 10.9.0+ (tested with 10.11.5)
- **Clients**: All official Jellyfin clients
  - Web interface
  - Mobile apps (iOS, Android)
  - TV apps (Roku, Fire TV, Android TV, etc.)
  - Desktop apps

## Migration from Older Jellyfin Versions

If you're upgrading from Jellyfin 10.8.x or earlier that had built-in PIN support:

1. Install this plugin
2. Users will need to have PINs set by administrators
3. Old PINs from previous versions will not be automatically migrated
4. Administrators should set new PINs for users who need them

## Contributing

Contributions are welcome! Areas for enhancement:

- Additional hash algorithms
- Configurable PIN length
- User self-service PIN management
- Two-factor authentication integration
- PIN complexity requirements

## License

This plugin is provided as-is for use with Jellyfin. Please comply with Jellyfin's licensing terms.

## Support

For issues, questions, or feature requests:
1. Check the Troubleshooting section
2. Review Jellyfin server logs
3. Open an issue on the project repository

## Changelog

### Version 1.0.0
- Initial release
- 4-digit PIN authentication
- Admin PIN management interface
- Rate limiting and lockout protection
- SHA256 PIN hashing
- Support for all Jellyfin clients
