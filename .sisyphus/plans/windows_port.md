# OpenWork Windows Port Plan

This plan details the steps required to make OpenWork fully compatible with Windows.

## 1. Prerequisites & Environment
- **Target OS**: Windows 10/11
- **Build Tools**: Visual Studio Build Tools (C++), Rust, Node.js
- **Runtime**: WebView2 (standard on modern Windows)

## 2. Configuration Updates (`src-tauri/tauri.conf.json`)
- [ ] Add `bundle.windows` configuration:
  - Configure `nsis` (preferred over MSI for auto-updates/UI).
  - Ensure `wix` is configured if MSI is needed.
  - Verify `icon` paths are valid for Windows (`.ico`).
- [ ] Update `package.json` scripts to ensure cross-platform compatibility (avoid `&&` if possible or use `concurrently`/npm hooks).

## 3. Rust Backend Refactoring (`src-tauri/src/lib.rs`)
### Path Resolution
- [ ] Replace manual `env::var("HOME")` / `XDG_CONFIG_HOME` checks with the `dirs` crate or Tauri's `path` scope.
- [ ] Update `resolve_opencode_config_path` to use `%APPDATA%/opencode/opencode.json` on Windows.
- [ ] Add Windows-specific candidate paths for `opencode` CLI:
  - `C:\Users\<User>\.opencode\bin\opencode.exe`
  - Scoop: `~\scoop\shims\opencode.exe`
  - Chocolatey: `C:\ProgramData\chocolatey\bin\opencode.exe`

### Process Spawning
- [ ] Fix `Command::new` calls for npm/pnpm/opkg:
  - On Windows, these are `.cmd` files.
  - Use `cmd /C <command>` wrapper or explicitly append `.cmd` extension.
  - Ensure `Command::new("bash")` is **NOT** called on Windows.

### Installation Logic (`engine_install`)
- [ ] Implement `#[cfg(windows)]` block for `engine_install`:
  - **Option A (Simpler)**: Open browser to `https://opencode.ai/install` (if no direct installer exists).
  - **Option B (Preferred)**: Run a PowerShell script:
    ```powershell
    iwr https://opencode.ai/install.ps1 -useb | iex
    ```
  - Remove the explicit "not supported" error message.

## 4. Frontend Adjustments (`src/App.tsx`)
- [ ] Update `engineDoctor` result display:
  - Show Windows-relevant paths in the "checked locations" list.
- [ ] Update Error Messages:
  - If on Windows, suggest "Install via PowerShell" or "Download Installer" instead of `brew` / `curl | bash`.
  - Update `startHost` error messages.
- [ ] Verify `pickDirectory` dialog works correctly (Tauri handles this, but good to double-check formatting).

## 5. Build & CI
- [ ] Add a Windows build job to GitHub Actions (if applicable).
- [ ] Test the build locally with `npm run tauri build -- --target x86_64-pc-windows-msvc`.

## 6. Testing Checklist
- [ ] **Install**: Does the app launch without crashing?
- [ ] **Detection**: Does it find an existing `opencode.exe`?
- [ ] **Install Flow**: Does the "Install OpenCode" button work (PowerShell/Link)?
- [ ] **Run**: Can it spawn `opencode serve` and connect?
- [ ] **Tools**: Do `opkg install` and plugins work (handling `.cmd` shims)?
