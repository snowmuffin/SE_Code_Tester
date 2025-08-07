# Space Engineers Plugin Template - Code Tester

A comprehensive template project for developing Space Engineers plugins that work with both the client game and dedicated servers. This project demonstrates best practices for plugin development including Harmony patching, configuration management, UI generation, and code verification.

## Features

- **Multi-Target Support**: Supports Client, Dedicated Server, and Torch server environments
- **Harmony Integration**: Advanced patching capabilities with code verification
- **Dynamic UI Generation**: Automatic settings screen generation from configuration properties
- **Configuration Management**: Persistent configuration with type-safe access
- **Code Change Detection**: Runtime verification to detect conflicting game updates
- **Comprehensive Logging**: Structured logging with multiple severity levels
- **Example Implementations**: Complete examples of patches, transpilers, and UI elements

## Project Structure

```
SE_Code_Tester/
├── Code_tester.sln              # Main solution file
├── Directory.Build.props        # MSBuild properties (game paths)
├── ClientPlugin/                # Client-side plugin
│   ├── Plugin.cs               # Main plugin entry point
│   ├── Config.cs               # Client configuration with UI attributes
│   ├── Settings/               # UI generation framework
│   └── deploy.bat              # Deployment script
├── DedicatedPlugin/            # Dedicated server plugin
│   ├── Plugin.cs               # Server plugin entry point
│   └── deploy.bat              # Deployment script
└── Shared/                     # Common code between projects
    ├── Config/                 # Configuration framework
    ├── Logging/                # Logging infrastructure
    ├── Patches/                # Example patches and helpers
    ├── Plugin/                 # Common plugin interfaces
    └── Tools/                  # Utility classes and helpers
```

## Prerequisites

- **Visual Studio 2019/2022** or **Rider** with C# support
- **.NET Framework 4.8.1**
- **Space Engineers** (Client and/or Dedicated Server)
- **NuGet Package Manager**

## Initial Setup

### 1. Clone and Configure Paths

1. Clone this repository to your development machine
2. **IMPORTANT**: Edit `Directory.Build.props` to match your Space Engineers installation paths:

```xml
<Project>
  <PropertyGroup>
    <!-- Update these paths to match your installation -->
    <Bin64>C:\Program Files (x86)\Steam\steamapps\common\SpaceEngineers\Bin64</Bin64>
    <Dedicated64>C:\Program Files (x86)\Steam\steamapps\common\SpaceEngineersDedicatedServer\DedicatedServer64</Dedicated64>
    <Torch>C:\Path\To\Your\Torch\Installation</Torch>
  </PropertyGroup>
</Project>
```

### 2. Manual Assembly References

⚠️ **CRITICAL**: This project requires manual assembly setup as it doesn't include automated dependency resolution.

You need to ensure all referenced assemblies exist in your Space Engineers installation directories:

#### Required Game Assemblies (from Bin64/DedicatedServer64):
- All VRage.* assemblies
- All Sandbox.* assemblies  
- All SpaceEngineers.* assemblies
- System.* runtime assemblies
- Third-party dependencies (HavokWrapper, NLog, ProtoBuf.Net, etc.)

#### NuGet Dependencies:
The following packages are managed via NuGet:
- `Lib.Harmony` (2.3.3) - For runtime patching
- `Newtonsoft.Json` (13.0.2) - For JSON serialization

### 3. Build Configuration

1. Open `Code_tester.sln` in Visual Studio
2. Ensure all assembly references resolve correctly
3. Build the solution in **Debug** or **Release** mode
4. The deployment scripts will automatically copy built assemblies to the appropriate plugin directories

## Development Guide

### Creating Your Plugin

1. **Rename the Project**: Update assembly names, namespaces, and plugin names throughout the codebase
2. **Configure Settings**: Modify `Config.cs` files to define your plugin's configuration options
3. **Implement Features**: Add your plugin logic in the `CustomUpdate()` methods
4. **Add Patches**: Create Harmony patches in the `Shared/Patches/` directory

### Configuration System

The plugin uses a sophisticated configuration system with automatic UI generation:

```csharp
[Checkbox(description: "Enable this feature")]
public bool FeatureEnabled { get; set; } = true;

[Slider(0f, 100f, 1f, SliderAttribute.SliderType.Float)]
public float SomeValue { get; set; } = 50f;

[Dropdown(description: "Select an option")]
public MyEnum SelectedOption { get; set; } = MyEnum.Default;
```

### Harmony Patching

Example of a method prefix patch:
```csharp
[HarmonyPrefix]
[HarmonyPatch(typeof(TargetClass), "MethodName")]
[EnsureCode("12345678")] // Hash verification
public static bool MethodPrefix()
{
    // Your patch logic here
    return true; // Continue to original method
}
```

Example of a transpiler patch:
```csharp
[HarmonyTranspiler]
[HarmonyPatch(typeof(TargetClass), "MethodName")]
[EnsureCode("87654321")]
private static IEnumerable<CodeInstruction> MethodTranspiler(IEnumerable<CodeInstruction> instructions)
{
    var il = instructions.ToList();
    il.RecordOriginalCode();
    
    // Modify IL code here
    
    il.RecordPatchedCode();
    return il;
}
```

### Code Verification

The `EnsureCode` attribute verifies that game methods haven't changed unexpectedly:
- Calculates hash of target method's IL code
- Compares against known-good hashes
- Prevents plugin from loading if verification fails
- Essential for maintaining compatibility across game updates

## Building and Deployment

### Automatic Deployment

The project includes automatic deployment via post-build events:

1. **Build** the solution
2. **Deploy scripts** automatically copy assemblies to:
   - `{GamePath}\Bin64\Plugins\Local\` (Client)
   - `{DedicatedPath}\DedicatedServer64\Plugins\Local\` (Server)

### Manual Deployment

If automatic deployment fails:

1. Copy `ClientPlugin\bin\{Configuration}\Code_tester.dll` to `{GamePath}\Bin64\Plugins\Local\`
2. Copy `DedicatedPlugin\bin\{Configuration}\Code_tester.dll` to `{DedicatedPath}\DedicatedServer64\Plugins\Local\`

## Testing and Debugging

### Client Testing
1. Launch Space Engineers
2. Check logs in `%AppData%\SpaceEngineers\Logs\`
3. Access plugin settings via the mod menu (if UI is implemented)

### Server Testing
1. Launch dedicated server
2. Check server logs for plugin loading messages
3. Monitor plugin behavior during gameplay

### Debugging
- Use `#if DEBUG` blocks for debug-only code
- The template includes debugger attachment support
- IL code recording helps with transpiler development

## Common Issues and Solutions

### Assembly Reference Errors
- Verify all game installation paths in `Directory.Build.props`
- Ensure Space Engineers is up to date
- Check that all referenced assemblies exist in the specified directories

### Patch Failures
- Update `EnsureCode` hashes after game updates
- Verify target methods still exist with expected signatures
- Check compatibility with other installed plugins

### Deployment Issues
- Run Visual Studio as Administrator if file access is denied
- Manually create plugin directories if they don't exist
- Check that game/server processes aren't locking the DLL files

## Advanced Features

### Settings UI Framework
The project includes a complete UI generation system that creates settings dialogs from configuration properties using attributes.

### Code Change Detection
Runtime verification system that detects when game code has changed, preventing crashes from outdated patches.

### Multi-Environment Support
Single codebase that compiles to different targets (Client/Dedicated) using conditional compilation.

### Transpiler Helpers
Utility methods for common IL manipulation tasks in transpiler patches.

## Contributing

When contributing to this template:

1. Follow the existing code style and patterns
2. Add appropriate documentation for new features
3. Include examples for complex functionality
4. Test changes across all supported environments

## License

This project is provided as a template for Space Engineers plugin development. Modify and use as needed for your own plugins.

## References

- [Space Engineers Modding Guide](https://github.com/KeenSoftwareHouse/SpaceEngineers)
- [Harmony Patching Documentation](https://harmony.pardeike.net/)
- [Example Performance Improvements](https://github.com/viktor-ferenczi/performance-improvements)

---

**Note**: This template requires manual setup of assembly references as it doesn't include automated dependency management. Ensure all required Space Engineers assemblies are available in your installation directories before building.
