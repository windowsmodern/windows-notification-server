# Windows Notification Server
First, I would like to express my gratitude and apologies to the author of the Glass Toasts project (https://github.com/AndreasVerhoeven/GlassToasts); this project enables the interception and suppression of Windows balloon notifications in favor of displaying custom notifications.

Second, I intend to implement a notification center-like feature based on this project for use in my "Sidebar" project (https://github.com/WindowsModern/sidebar), and I have designed a generalized data model and notification retrieval mechanism for this purpose.

This project was developed with AI assistance and complies with the licensing requirements and scope of the Glass Toasts project.

This project can serve as a reference for sharing and learning purposes.

However, it cannot intercept Toast Notifications used in Windows 8 and later versions; it is intended for intercepting notifications on older systems.

## Windows XP support

`NotifyServer.exe` is meant to run on Windows XP as well, which imposes two build
constraints:

1. **No static imports of Vista+ APIs.** `CancelIoEx` only exists on Vista and later;
   importing it directly makes the XP loader reject the whole executable with
   `无法定位程序输入点 CancelIoEx 于动态链接库 KERNEL32.dll 上`. `CancelPendingIo()` in
   `NotifyClient/main.cpp` therefore resolves `CancelIoEx` with `GetProcAddress` and falls
   back to `CancelIo` on XP, which is sufficient because the pending `ConnectNamedPipe`
   was issued by the same thread. Do not call `CancelIoEx` directly anywhere else.
2. **Link the CRT statically.** The `Win32` and `x64` configurations use
   `<RuntimeLibrary>MultiThreaded</RuntimeLibrary>` / `MultiThreadedDebug` (`/MT`, `/MTd`)
   together with the `v140_xp` toolset. Universal CRT redistributables released after
   VS 2015 Update 3 (16.7) no longer support Windows XP, so depending on `MSVCP140.dll`,
   `VCRUNTIME140.dll` or the `api-ms-win-crt-*.dll` forwarders would make the binaries
   fail to start there.
