# Call Control API for Linux

> Source: <https://www.3cx.com/docs/call-api-linux/> — last updated 12 September 2024

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
  - [Linux](#linux)
- [Deploying the Sample Scripts](#deploying-the-sample-scripts)
  - [Sample 1 - Extension Status](#sample-1---extension-status)
  - [Sample 2 - ForcePBXHours](#sample-2---forcepbxhours)
  - [Sample 3 - Personal Parking with Auto Return](#sample-3---personal-parking-with-auto-return)
  - [Sample 4 - Queue Agent Status](#sample-4---queue-agent-status)
  - [Sample 5 - User Input IVR](#sample-5---user-input-ivr)
- [See also](#see-also)

## Introduction

The new Call Control API for V20 can be downloaded [here](https://downloads-global.3cx.com/downloads/misc/callcontrolapi/3CXCallControlAPI_v20.zip).

The zip file includes:

- **`Help`** folder with HTML documentation for the API. It details all methods available for your custom applications to interact with 3CX. Browse it by opening `index.html` in your browser.
- **`OMSamples`** folder with C# samples demonstrating how to use the API.

## Prerequisites

> [!IMPORTANT]
> The following is meant to be run in a **development environment**, not on a production 3CX machine.

### Linux

As a prerequisite, you will need a self-hosted or on-premise **3CX v20 for Debian 12**.

1. Log in to SSH as `root` using your preferred SSH client and install the .NET Core 8.0 SDK:

   ```bash
   wget https://packages.microsoft.com/config/debian/12/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
   dpkg -i packages-microsoft-prod.deb
   rm packages-microsoft-prod.deb

   apt-get update && apt-get install -y dotnet-sdk-8.0
   ```

   For more information, see [Install .NET on Debian 12](https://learn.microsoft.com/en-us/dotnet/core/install/linux-debian#debian-12).

2. Download the Call Control API:

   ```bash
   wget https://downloads-global.3cx.com/downloads/misc/callcontrolapi/3CXCallControlAPI_v20.zip
   ```

3. Extract it:

   ```bash
   apt install unzip
   unzip 3CX*
   ```

4. Go into the `OMSamples` folder:

   ```bash
   cd OMSamples
   ```

5. Compile:

   ```bash
   dotnet build OMSamplesCore.csproj
   ```

   If successful, it will say:

   ```text
   Build succeeded.
       0 Warning(s)
       0 Error(s)
   ```

6. Copy the `Scripts` folder into the output folder:

   ```bash
   cp Scripts bin/Debug/net8.0 -r
   ```

7. Go into the output folder:

   ```bash
   cd bin/Debug/net8.0
   ```

8. Run the sample executable:

   ```bash
   ./OMSamplesCore
   ```

## Deploying the Sample Scripts

Once the sample executable is running, you are in an interactive shell that lets you deploy sample scripts to your local 3CX.

After each deployment, log into the Web Client as the **System Owner** and go to **Admin > Advanced > Call Flow Apps** to see the newly created and registered CFDs.

### Sample 1 - Extension Status

Deploy:

```text
>scriptdev deployall folder=Scripts/ExtensionStatus
```

It should report that `#0` to `#4` have been updated. In Call Flow Apps you will see that **5 CFDs** have been created and registered.

These dial codes can be called from anyone's dialer to change their current user status.

> [!NOTE]
> Temporary status override will not be reset.

**Usage:**

| Dial code | Resulting status |
|-----------|------------------|
| `#0` | Available |
| `#1` | Away (watch the presence indicator in the top-right corner of the Web Client) |
| `#2` | Do not Disturb |
| `#3` | Lunch (Custom 1) |
| `#4` | Business Trip (Custom 2) |

Dial the code and hang up.

### Sample 2 - ForcePBXHours

> Available in V20 Update 1 or 2.

Deploy:

```text
>scriptdev deployall folder=Scripts/ForcePBXHours
```

It should report that `#60` to `#64` have been updated. In Call Flow Apps you will see **5 more CFDs** created and registered (Update 1 or 2 required, otherwise they show as unregistered).

These dial codes can be called from anyone's dialer to switch the whole system to specific hours.

In V20 the legacy in office / out of office dial code has been removed. This sample is a RoutingPoint that implements its equivalent in a multi-department model.

**Usage:**

| Dial code | Action |
|-----------|--------|
| `#60` | Reset all departments to default hours operation |
| `#61` | Force all departments to In-office hours |
| `#62` | Force all departments to Out-of-office hours |
| `#63` | Force all departments to Break time |
| `#64` | Force all departments to Holiday time |

Dial the code and hang up.

### Sample 3 - Personal Parking with Auto Return

Deploy:

```text
>scriptdev deployall folder=Scripts/PersonalParkingWithAutoReturn
```

It should report that `#` has been updated. In Call Flow Apps you will see **1 more CFD** created and registered.

This dial code can be called from anyone's dialer to park calls. Parked calls are put on hold with parking music and will try to return to the extension when it is available (no longer in a call and not in DND).

**Usage:**

1. Have an inbound call from caller A to your extension, then blind transfer it to `#`.
2. Caller A will hear music. Hang up on your end.
3. Put yourself in a call (e.g. to special menu `999`), then hang up after a while.
4. The call with caller A will be returned to you automatically.

### Sample 4 - Queue Agent Status

Deploy:

```text
>scriptdev deployall folder=Scripts/QueueAgentStatus
```

It should report that `#30` and `#31` have been updated. In Call Flow Apps you will see **2 more CFDs** created and registered.

These dial codes can be called from anyone's dialer to change their queue login status.

**Usage:**

1. Add a queue from **Call Handling** and add yourself as an agent.
2. Open the **Panel** view of your Web Client to see your agent status.
3. Dial `#31` to log out of all queues where your extension is an agent. Hang up and watch the **"Queue logged in to"** column change.
4. Dial `#30` to log in to all queues where your extension is an agent.

### Sample 5 - User Input IVR

> Available in V20 Update 1 or 2.

Deploy:

```text
>scriptdev deployall folder=Scripts/UserInputIVR
```

It should report that `#8877` has been updated. In Call Flow Apps you will see **1 more CFD** created and registered.

This dial code can be called from anyone's dialer to simulate a DTMF input IVR, where a list of predefined PINs is mapped to a list of routes the caller is transferred to.

See `Scripts/UserInputIVR/readme.txt` for more information.

## See also

- Call Control API for Windows
- Google Cloud Storage & Speech API
- Creating a Call Processing Script
- Call Processing Script for DTMF Input
- How to use 3CX configuration REST API
