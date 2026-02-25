# AGENTS.md - Spotube DAB Music Plugin

## Project Overview

This is a Spotube audio source plugin written in [Hetu](https://hetu-lang.github.io/hetu-lang/) (a Dart-like scripting language). The plugin provides integration with DAB Music (dab.yeet.su) as an audio source for Spotube.

## Build Commands

```bash
# Compile the plugin (Hetu -> bytecode)
make compile
# Output: build/plugin.out (copied to example/assets/bytecode/)

# Create distributable plugin archive
make archive
# Output: build/plugin.smplug

# Full build (compile + archive)
make
```

### Requirements
- Flutter/Dart SDK (for `hetu` CLI tool)
- Install Hetu dev tools: `dart pub global activate hetu_script_dev_tools`

## Testing

**No test framework is currently configured for this project.**

If tests are added in the future, they would likely use Hetu's built-in testing capabilities or Dart test framework.

## Code Style Guidelines

### File Structure
- Main entry: `src/plugin.ht`
- Segments in `src/segments/`: `core.ht`, `auth.ht`, `audio_source.ht`
- One class per file is preferred; export only what other files need

### Imports
```hetu
import "module:std" as std
import "module:spotube_plugin" as spotube
import { AuthEndpoint } from "./segments/auth.ht"
import { AudioSourceEndpoint } from './segments/audio_source.ht'
```

### Naming Conventions
- **Files**: lowercase with underscores: `core.ht`, `audio_source.ht`
- **Classes**: PascalCase: `class CorePlugin`, `class AuthEndpoint`
- **Functions/methods**: camelCase: `fun checkUpdate()`, `fun login()`
- **Variables**: camelCase: `var client`, `var credentials`
- **Constants**: camelCase with `final`: `final api: HttpClient`
- **Private members**: prefix with underscore (optional): `_internalMethod()`

### Type Annotations
Always include type annotations for variables, parameters, and return types:
```hetu
var client: HttpClient
var credentials: Map
fun checkUpdate(currentConfig: Map) -> Future
fun login(email: string, password: string) -> Future
```

### Common Types
- `string`, `bool`, `int`, `float`
- `List`, `Map` (generic: `List<Map>`, `Map<string, int>`)
- `Future` for async operations
- `Stream`, `StreamController` for reactive streams

### Class Syntax
```hetu
class MyClass {
  var property: Type
  final constant: Type
  
  construct (this.property, this.constant)
  
  fun method(param: Type) -> ReturnType {
    // body
  }
  
  get derivedProperty -> Type {
    return computeValue()
  }
}
```

### Map/JSON Operations
Use `.toJson()` to convert Maps to JSON-serializable format:
```hetu
var data = { "key": "value" }.toJson()
var headers = { "Cookie": auth.getCookieHeaderValue() }.toJson()
```

### Async Operations
```hetu
fun fetchData() -> Future {
  return client.get_req("/api/endpoint").then((res) {
    return res.data
  })
}
```

### Error Handling
```hetu
// Throw exceptions with descriptive messages
throw "Invalid email or password"
throw "No download URL found for the plugin update"

// Handle with try-catch (if needed)
try {
  // code
} catch (e) {
  // handle error
}
```

### Null Safety
Use null-aware operators:
```hetu
credentials?["session"]  // returns null if credentials is null
credentials ?? defaultValue  // uses default if null
```

### Getters
```hetu
get authStateStream -> Stream => controller.stream

get support -> string {
  return "Support text"
}
```

### Documentation Comments
Use `///` for documentation:
```hetu
/// Checks for updates to the plugin.
/// [currentConfig] is just plugin.json's file content.
fun checkUpdate(currentConfig: Map) -> Future {
  // ...
}
```

### Plugin Structure
The main plugin class must:
1. Be exported with the name specified in `plugin.json` (`DABMusicAudioSourcePlugin`)
2. Have `auth`, `audioSource`, and `core` properties
3. Implement required endpoints based on `abilities` in `plugin.json`

## Additional Notes

- This is a Spotube plugin using the plugin API v2.0.0
- The plugin uses HTTP client with base URL `https://dab.yeet.su/api`
- Authentication uses cookies stored in LocalStorage
- Audio streams provide both FLAC (lossless) and AAC (lossy) options
