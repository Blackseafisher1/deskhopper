# DeskHopper

DeskHopper is a lightweight, background utility for Windows 10/11 that enhances your virtual desktop workflow. It allows you to quickly switch between virtual desktops and move your active windows to different desktops using global hotkeys. It also remembers the last active window on each desktop and attempts to focus it upon switching.

## ✨ Features

* **Switch Virtual Desktops**: Instantly switch to virtual desktops 1 through 10.
    * `Right Ctrl + 1` to `Right Ctrl + 9` for desktops 1-9.
    * `Right Ctrl + 0` for desktop 10.
* **Move Active Window to Virtual Desktop**: Seamlessly move your currently focused window to a specific virtual desktop.
    * `Right Ctrl + Shift + 1` to `Right Ctrl + Shift + 9` to move the window to desktops 1-9.
    * `Right Ctrl + Shift + 0` to move the window to desktop 10.
* **Next / Previous Desktop**: `Ctrl + Win + Left/Right Arrow` overrides Windows' default hotkey for an instant switch (no sluggish animation), wrapping around at the first/last desktop.
* **Move Window to Next / Previous Desktop**: `Ctrl + Alt + Left/Right Arrow` moves the focused window to the previous/next desktop (wraps around).
* **Smart Focus**: Remembers the last active window on each desktop and attempts to restore focus to it when you switch back.
* **Background Operation**: Runs silently in the background without a console window.
* **System Tray Control**:
    * Accessible via a system tray icon.
    * Right-click context menu with:
        * "About DeskHopper": Displays application information.
        * "Exit": Gracefully closes the application.
* **Desktop Creation**: If a target desktop for switching or moving a window doesn't exist, DeskHopper will create the necessary desktops up to the target number.

## 🚀 Getting Started

### Prerequisites

* **Windows 10/11**
* **Rust Toolchain**: Install from [rustup.rs](https://rustup.rs/).
* **Visual Studio Build Tools**: Required for compiling. Ensure the "Desktop development with C++" workload is installed. You can install it via the Visual Studio Installer or using Winget:
    ```powershell
    winget install Microsoft.VisualStudio.2022.BuildTools --override "--add Microsoft.VisualStudio.Workload.NativeDesktop --includeRecommended"
    ```
    (If you are on an ARM64 Windows device, ensure the ARM64 C++ tools are included).

### Building from Source

1.  **Clone the repository** (replace with your actual repository URL if you have one):
    ```bash
    git clone <your-repository-url>
    cd deskhopper 
    ```
    If you don't have a repository yet, simply navigate to the directory where you have the source code.

2.  **Build the application**:
    ```bash
    cargo build --release
    ```

3.  **Locate the executable**:
    The compiled executable will be found at `target/release/deskhopper.exe`.

### Running DeskHopper

1.  Navigate to the `target/release/` directory.
2.  Run `deskhopper.exe`.
3.  The application will start in the background, and you should see its icon in the system tray.

### Hotkeys

* **Switch to Desktop X**: `Right Ctrl + <Number>` (where `0` maps to desktop 10)
* **Move Active Window to Desktop X**: `Right Ctrl + Shift + <Number>` (where `0` maps to desktop 10)
* **Next / Previous Desktop**: `Ctrl + Win + Left/Right Arrow` — overrides Windows' default hotkey for an instant switch without the sluggish animation. Wraps around at the first/last desktop.
* **Move Window to Next / Previous Desktop**: `Ctrl + Alt + Left/Right Arrow` — moves the focused window to the previous/next desktop (wraps around).

### Running at Startup (Recommended)

To have DeskHopper start automatically when Windows boots up:

1.  Press `Win + R` to open the Run dialog.
2.  Type `shell:startup` and press Enter. This will open your Startup folder.
3.  Create a shortcut to `deskhopper.exe` (from your `target/release/` folder).
4.  Place this shortcut into the Startup folder.

### Running as Administrator (Recommended)

DeskHopper intercepts `Ctrl + Win + Left/Right` with a low-level keyboard hook. Because of Windows' User Interface Privilege Isolation (UIPI), a **non-elevated** DeskHopper cannot see keystrokes while an **elevated** window (e.g. Task Manager) is focused — in that case Windows' default switch takes over. Running DeskHopper elevated fixes this. The `Right Ctrl + <Number>` hotkeys work regardless because they use `RegisterHotKey`.

The recommended way is a Task Scheduler task. You answer the UAC prompt **only once** when the task is created; afterwards DeskHopper starts elevated silently at every logon.

**Option A – current user** (the user running the command must have admin privileges):

```powershell
schtasks /create /tn "DeskHopper" /tr "C:\full\path\to\deskhopper.exe" /sc onlogon /rl highest /f
```

**Option B – any user (all users)**, via the Task Scheduler GUI:

1.  `Win + R` → `taskschd.msc`
2.  Task Scheduler Library → right-click → **Create Task**
3.  **General** tab: give it a name and check **Run with highest privileges**
4.  **Triggers** tab → New → Begin the task: **At log on** → select **Any user** (or your user for this user only)
5.  **Actions** tab → New → Start a program → browse to `deskhopper.exe`, and set **Start in (optional)** to the folder containing `deskhopper.exe` (its working directory)
6.  **Conditions** tab → uncheck **Start the task only if the computer is on AC power** and **Stop if the computer switches to battery power**
7.  OK (answer the UAC prompt once)

Notes:

* **Recommended: install the binary to a stable location** instead of pointing the task at a build folder. Create e.g. `C:\Program Files\Deskhopper\`, copy `deskhopper.exe` there, and point the task at that path (set **Start in** to `C:\Program Files\Deskhopper`). Build/target folders can be wiped by rebuilds, which would break the task.
* `deskhopper.exe` must stay at the configured path.
* To remove the task later: `schtasks /delete /tn "DeskHopper" /f`
* The task's XML lives at `C:\Windows\System32\Tasks\DeskHopper` but is system-protected — always edit via the GUI, `schtasks.exe`, or PowerShell.

## ⚙️ Configuration

* **Icon**: As mentioned, place your `icon.ico` in the project root before building.
* **Hotkeys**: Hotkey combinations are defined in `src/main.rs` within the `register_hotkeys` function. You can modify the source code to change them if desired, then recompile.

## 🐛 Troubleshooting

* **Linker Errors during build (`link.exe` not found)**: This usually means the Visual Studio Build Tools (Desktop development with C++) are not installed correctly or not found in your PATH. Ensure they are installed and try restarting your terminal or computer.
* **Hotkeys Not Working**:
    * Ensure DeskHopper is running (check the system tray).
    * Check for conflicts with other applications that might be using the same global hotkeys.
* **Tray Icon Menu Actions Not Working**: Ensure the latest version of the code is compiled, as there were several iterations to get this working reliably.

## 🤝 Contributing

Contributions are welcome! If you'd like to contribute, please feel free to fork the repository, make your changes, and submit a pull request. You can also open issues for bugs or feature requests.

