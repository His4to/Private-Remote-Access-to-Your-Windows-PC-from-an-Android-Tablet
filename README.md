# Remote Access to Your Windows PC from an Android Tablet (RustDesk + Tailscale)

This guide walks through setting up free, secure remote access to a Windows 11 PC from an Android tablet. I use this for video editing and for grabbing my Windows device when I'm on the go. The setup used here is a Windows 11 PC as the host and a Samsung Galaxy Tab S8+ as the client.

RustDesk without a Pro plan has limitations, so the goal is a direct PC to Android connection. We do this in two phases:

1. Get RustDesk working over your local network (to confirm everything functions).
2. Add Tailscale so it works from anywhere, with no router changes.

---

## What You Need

- A Windows 11 PC (the host, the machine you want to control)
- An Android device (the client, a Samsung Tab S8+ in my case)
- A Google account (used to sign in to Tailscale)
- Both devices on the same Wi-Fi network for the first test

---

## Phase 1: Install RustDesk and Test on Your Local Network

### Step 1: Install RustDesk on the Windows PC

1. On your PC, go to https://rustdesk.com/download
2. Find the Windows row and choose the architecture that matches your device. For a standard Windows 11 PC, pick **x86-64 (64-bit)**.
   - I chose this because I only want to sync one device and don't need the more complex installation options.
3. Run the downloaded installer and finish the setup. Use the regular installer instead of the portable version so RustDesk can run as a background service and allow unattended access later.
4. Open RustDesk. You should see a **9-digit ID** on the left side. Keep this window open, you will need the ID soon.

### Step 2: Install RustDesk on the Android tablet

1. On the same download page, go to the **Android** column in the same row. For the Tab S8+, the **arm64-v8a** APK is the right one (most modern Android phones and tablets use this).
2. The download is an open source APK file. Android blocks installs from outside the Play Store by default, so you need to allow it:
   - Open **Settings**, then **Apps**, then **Special access** (or search "Install unknown apps").
   - Select the browser you used to download the APK (for example, Chrome or Samsung Internet).
   - Turn on **Allow from this source**.
   - Menu names vary a little by brand, but this applies to most Android devices since the APK is a universal installer.
3. Open the downloaded APK from your notifications or your Downloads folder and tap **Install**.
4. Open RustDesk on the tablet and grant the permissions it asks for.

> Tip: Once the install is done, you can turn the "install unknown apps" permission back off for your browser.

### Step 3: Test the connection on your local network

This setup works mainly over your local network at first, so this step confirms everything is functioning.

1. Make sure the PC and the tablet are connected to the **same Wi-Fi network**.
2. On the PC, check the RustDesk window. At the bottom it shows the service status. If it says **"Service is not running"**, click **Start Service** at the lowermost part of the window and wait until it says **Ready**.
3. On the tablet, open RustDesk and type the **9-digit ID** shown on your PC into the remote ID field.
4. Tap **Connect**.
5. On the PC, accept the connection request, or enter the one-time password if you have that enabled. Since this is only a test, you can choose to have the one-time password on or off.
6. If you can see and control your PC from the tablet, RustDesk works. Move on to Phase 2.

> Note: "Start Service" on the Android app is only needed if you want to control the tablet itself. For controlling the PC from the tablet, the PC is the one that needs to be running RustDesk.

---

## Phase 2: Add Tailscale for Secure Access from Anywhere

Combining Tailscale and RustDesk gives you a highly secure remote desktop setup. Tailscale builds an encrypted peer-to-peer network (called a Tailnet) between your devices, so you can bypass RustDesk's public relay servers and connect directly without opening ports on your router. Traffic is routed through WireGuard's encrypted tunnels, which makes the connection responsive and private.

### Step 4: Install Tailscale on both devices

1. **On the PC:** download Tailscale for Windows from https://tailscale.com/download and install it.
2. **On the tablet:** install Tailscale from the Google Play Store.
3. Open Tailscale on each device and sign in with **the same Google account** on both. This puts both devices on your private Tailnet.
4. Make sure Tailscale is toggled **on** (connected) on both devices at the same time.

### Step 5: Copy the PC's Tailscale IP address

1. On the PC, open the Tailscale app, or go to the Tailscale Admin Console (https://login.tailscale.com/admin/machines) in your browser.
2. Find your Windows PC in the machine list.
3. Copy its **Tailscale IPv4 address**. It looks like `100.x.x.x`.

> Important: You must use the actual numerical IP address. RustDesk does not recognize Tailscale MagicDNS names (the device-name style addresses).

### Step 6: Check that the devices can see each other

1. On the tablet, open the Tailscale app.
2. **Long press** your desktop in the device list.
3. Choose **Ping** to confirm it responds. If the ping succeeds, the two devices are talking over your Tailnet.

### Step 7: Configure RustDesk on the PC (the host)

You need to tell RustDesk to accept direct network connections instead of waiting for a public relay.

1. Open RustDesk on the PC.
2. Click the **three-dot menu** next to your RustDesk ID and open **Settings**.
3. Go to the **Security** tab.
4. Click **Unlock security settings** at the top. This needs administrator privileges, so approve the Windows prompt.
5. Scroll down and check the box for **Enable direct IP access**.
6. Scroll to the **Password** section and set a **permanent password**. This lets you connect without a rotating temporary PIN, which is what makes unattended access possible.
7. If Windows Firewall asks whether to allow RustDesk through, click **Allow**.

### Step 8: Connect from the tablet

1. Confirm Tailscale is active on both devices.
2. Open RustDesk on the tablet.
3. In the remote ID field, **paste the PC's Tailscale IP address** instead of the 9-digit RustDesk ID.
4. Tap **Connect**.
5. Enter the **permanent password** you created in Step 7.

You are now connected through your private, encrypted Tailnet.

---

## Using It Day to Day

- Make sure the PC is **on, awake, and connected to the internet**, with Tailscale and RustDesk running.
- Tailscale must be active on both devices before you connect.
- You can now freely connect to your desktop anytime. I use it for video editing and whenever I need my Windows device while on the go.
- **Wi-Fi latency is the main issue.** A weak or congested connection on either end will make the screen lag, but it should be fine on a stable network.

---

## Troubleshooting

- **RustDesk says "Service is not running":** Click Start Service and wait for the status to show Ready.
- **Connection fails with the Tailscale IP:** Check that Tailscale is on for both devices, and run the ping test from Step 6. Also confirm "Enable direct IP access" is checked on the PC.
- **Using a device name instead of an IP:** RustDesk will not accept MagicDNS names, so use the `100.x.x.x` address.
- **Wrong password error:** Re-open Settings, then Security on the PC and set the permanent password again.
- **APK will not install:** Re-check the "Install unknown apps" permission for the app you downloaded it with.
- **PC does not respond when you are away:** Check that Windows is not set to sleep. Adjust it in Settings, System, Power.

---

## Quick Summary

1. Install RustDesk on the PC (Windows, x86-64) and the tablet (Android APK).
2. Test the connection over your local Wi-Fi using the RustDesk ID.
3. Install Tailscale on both devices and sign in with the same Google account.
4. Copy the PC's Tailscale IP address.
5. On the PC, enable direct IP access and set a permanent password.
6. On the tablet, connect using the Tailscale IP and the permanent password.
