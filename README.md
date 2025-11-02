```markdown
# SE_Code_Tester - Space Engineers Plugin Template

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Build Status](https://img.shields.io/badge/build-passing-brightgreen.svg)

SE_Code_Tester is a comprehensive template project designed for developing plugins for the popular game **Space Engineers**. This project is tailored to work seamlessly with both the **client game** and **dedicated servers**. It serves as an exemplary guide for plugin development, showcasing best practices such as Harmony patching, configuration management, dynamic UI generation, and robust code verification.

## Features

- **Multi-Target Support**: Effortlessly supports Client, Dedicated Server, and Torch server environments, enabling a wide range of plugin deployment.
- **Harmony Integration**: Advanced patching capabilities that enhance game functionality while ensuring code verification, allowing for safe modifications.
- **Dynamic UI Generation**: Automatically generates settings screens from configuration properties, streamlining user interactions.
- **Configuration Management**: Provides persistent configuration with type-safe access for better user experience and easier maintenance.
- **Code Change Detection**: Implements runtime verification to identify conflicting game updates, ensuring that plugins remain operational across different game versions.
- **Comprehensive Logging**: Features structured logging with multiple severity levels, aiding in debugging and monitoring plugin behavior.
- **Example Implementations**: Includes complete examples of patches, transpilers, and UI elements, serving as practical references for developers.

## Project Structure

The project is organized into a clean and manageable structure, enabling easy navigation and development:

```
SE_Code_Tester/
├── Code_tester.sln              # Main solution file
├── Directory.Build.props        # MSBuild properties for game paths
├── ClientPlugin/                # Client-side plugin directory
│   ├── Plugin.cs                # Main entry point for the client plugin
│   ├── Config.cs                # Configuration class with UI attributes
│   ├── Settings/                # UI generation framework
│   └── deploy.bat               # Deployment script for clients
├── DedicatedPlugin/             # Dedicated server plugin directory
│   ├── Plugin.cs                # Main entry point for the server plugin
│   └── deploy.bat               # Deployment script for servers
└── Shared/                      # Common code that can be utilized by both plugins
    ├── Config/                  # Configuration framework with shared settings
    ├── Logging/                 # Logging infrastructure for structured output
    ├── Patches/                 # Example patches and helper classes
    ├── Plugin/                  # Common plugin interfaces for client and server
    └── Tools/                   # Utility classes and helpers
```

## Prerequisites

To get started with SE_Code_Tester, ensure that your development environment meets the following requirements:

- **Visual Studio 2019/2022** or **Rider** with C# support
- **.NET Framework 4.8.1**
- **Space Engineers** (Client and/or Dedicated Server)
- **NuGet Package Manager**

## Initial Setup

### 1. Clone and Configure Paths

1. Clone this repository to your development machine.
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

⚠️ **CRITICAL**: This project requires manual assembly setup as it does not include automated dependency resolution. Ensure all referenced assemblies exist in your Space Engineers installation directories:

#### Required Game Assemblies (from Bin64/DedicatedServer64):
- All `VRage.*` assemblies
- All `Sandbox.*` assemblies  
- All `SpaceEngineers.*` assemblies
- System.* runtime assemblies
- Third-party dependencies (e.g., HavokWrapper, NLog, ProtoBuf.Net, etc.)

#### NuGet Dependencies:
The following packages are managed via NuGet:
- `Lib.Harmony` (2.3.3) - For runtime patching
- `Newtonsoft.Json` (13.0.2) - For JSON serialization

### 3. Build Configuration

1. Open `Code_tester.sln` in Visual Studio.
2. Ensure all assembly references resolve correctly.
3. Build the solution in **Debug** or **Release** mode.
4. The deployment scripts will automatically copy built assemblies to the appropriate plugin directories.

## Development Guide

### Creating Your Plugin

1. **Rename the Project**: Update assembly names, namespaces, and plugin names throughout the codebase to reflect your plugin's purpose.
2. **Configure Settings**: Modify `Config.cs` files to define your plugin's configuration options, such as UI attributes and default values.
3. **Implement Features**: Add your plugin logic in the `CustomUpdate()` methods or other relevant classes.
4. **Add Patches**: Create Harmony patches in the `Shared/Patches/` directory, ensuring they are properly documented and tested.

### Configuration System

The plugin employs a sophisticated configuration system with automatic UI generation, allowing for intuitive user settings management:

```csharp
[Checkbox(description: "Enable this feature")]
public bool FeatureEnabled { get; set; } = true;

[Slider(0f, 100f, 1f, SliderAttribute.SliderType.Float)]
public float SomeValue { get; set; } = 50f;

[Dropdown(description: "Select an option")]
public MyEnum SelectedOption { get; set; } = MyEnum.Default;
```

### Harmony Patching Example

Here’s a quick look at how to implement a Harmony patch:

**Method Prefix Patch Example**:
```csharp
[HarmonyPrefix]
[HarmonyPatch(typeof(TargetClass), "MethodName")]
[EnsureCode("12345678")] // Hash verification for safety
public static bool MethodPrefix()
{
    // Your patch logic here
    return true; // Continue to the original method
}
```

**Transpiler Patch Example**:
```csharp
[HarmonyTranspiler]
[HarmonyPatch(typeof(TargetClass), "MethodName")]
[EnsureCode("87654321")]
private static IEnumerable<CodeInstruction> Transpiler(IEnumerable<CodeInstruction> instructions)
{
    // Your transpiler logic here
}
```

## Contributing

We welcome contributions to SE_Code_Tester! If you have suggestions, improvements, or fixes, please feel free to submit a pull request. Ensure that your contributions adhere to the project's coding standards and include appropriate documentation.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more information.

---

**For detailed usage instructions, refer to the examples provided in the project folders or the inline comments within the code files. Happy coding!**
```