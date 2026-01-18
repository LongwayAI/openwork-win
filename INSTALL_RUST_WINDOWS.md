# Installing Rust and Cargo on Windows

To run this project, you need the Rust toolchain installed. Follow these steps to set it up on Windows:

### 1. Download the Installer
Go to [**rustup.rs**](https://rustup.rs/) and download `rustup-init.exe`.

### 2. Run the Installer
Launch `rustup-init.exe`. You will see a terminal window with installation options.

### 3. Install Visual Studio Build Tools (Crucial)
Rust requires the C++ build tools for Windows to compile Tauri and other dependencies.
- If the installer detects they are missing, it will prompt you to install them.
- Choose the option to install **"Desktop development with C++"** in the Visual Studio Installer.
- Ensure the following components are selected:
    - **MSVC v143** - VS 2022 C++ x64/x86 build tools (or latest)
    - **Windows 11 SDK** (or Windows 10 SDK)

### 4. Complete Rust Installation
- Back in the `rustup-init` terminal, proceed with the default installation (usually option `1`).

### 5. Restart Your Terminal
After installation, **close all open PowerShell or Command Prompt windows** and open a new one to refresh the PATH environment variable.

### 6. Verify Installation
Run the following commands to ensure everything is working:
```powershell
rustc --version
cargo --version
```

If you see version numbers, you are ready to build the project!
