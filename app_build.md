# Building and Installing XDM

Xtreme Download Manager (XDM) is a desktop application primarily built with .NET. It supports Windows (via WPF) and Linux (via GTK).

Here is the process to build or install the application from source for both platforms.

---

## 1. Windows Build (WPF)

The Windows version is built using C# with Windows Presentation Foundation (WPF). 

### Prerequisites
- **Visual Studio 2022** (or JetBrains Rider / Visual Studio Code with C# Dev Kit)
- **.NET 6.0 SDK** (or later, as specified in the CI configuration)
- Optionally, **.NET Framework 4.7.2** Developer Pack (if you intend to target legacy Windows compatibility)

### Build Instructions

1. Clone the repository and navigate to the project directory:
   ```bash
   git clone https://github.com/subhra74/xdm.git
   cd xdm/app/XDM/XDM.Wpf.UI
   ```

2. Restore the necessary dependencies:
   ```bash
   dotnet restore
   ```

3. Build the application:
   ```bash
   dotnet build --configuration Release
   ```

4. Alternatively, open the main solution file [app/XDM/XDM_CoreFx.sln](file:///f:/Node/anz-download-manager/app/XDM/XDM_CoreFx.sln) in Visual Studio.
   - Set **`XDM.Wpf.UI`** as the Startup Project.
   - Select the **Release** configuration and build the solution (Ctrl+Shift+B).

The compiled executable `xdm-app.exe` will be located in the `bin/Release/` folder (or architecture-specific subfolder like `bin/Release/net6.0-windows/`).

---

## 2. Linux Build (GTK)

The Linux version is built using C# with GTK. The build process involves compiling the .NET application and then using provided bash scripts to package it into native Linux installers (`.deb`, `.rpm`, or Arch `.pkg`).

### Prerequisites
- **.NET 6.0 SDK**
- A Linux environment with `bash`
- `dpkg-deb` (for Debian/Ubuntu `.deb` packages)
- `rpmbuild` (if building `.rpm` for Fedora)
- `makepkg` (if building for Arch Linux)

### Build and Package Instructions

1. Navigate to the GTK UI directory:
   ```bash
   cd app/XDM/XDM.Gtk.UI
   ```

2. Publish the .NET application as a self-contained or framework-dependent release:
   ```bash
   dotnet publish -c Release -r linux-x64 --self-contained true
   ```

3. **Prepare the Packaging Directory:**
   The packaging scripts expect the compiled binaries to be placed in a specific directory.
   Copy the contents of your publish output directory (e.g., `bin/Release/net6.0/linux-x64/publish/`) into the `app/packaging/binary-source/` folder.
   
   Ensure that the `xdm-app` executable and the `XDM.App.Host` directory exist within `binary-source`.

4. **Run the Packaging Script:**
   Navigate to the `app/packaging/` directory:
   ```bash
   cd ../../packaging/
   ```
   Execute the script corresponding to your Linux distribution:
   
   - **For Debian/Ubuntu:**
     ```bash
     ./make-deb-pkg
     ```
     This will generate an `xdman_gtk_XXX_amd64.deb` file.

   - **For Arch Linux:**
     ```bash
     ./make-arch-pkg
     ```
     This will generate a `.pkg.tar.xz` file.

   - **For Fedora/RHEL:**
     ```bash
     ./make-rpm-pkg
     ```
     This will generate an `.rpm` file.

5. **Install:**
   Use your distribution's package manager to install the generated package. For example, on Ubuntu:
   ```bash
   sudo dpkg -i xdman_gtk_8.0.9_amd64.deb
   sudo apt-get install -f # To resolve any missing dependencies like GTK3 or FFmpeg
   ```
