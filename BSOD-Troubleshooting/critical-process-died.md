
# 1. CRITICAL_PROCESS_DIED

## What the user experiences

The system suddenly crashes and restarts. Windows displays a blue screen indicating that a critical system process has stopped unexpectedly.

---

## Step-by-step troubleshooting

### Step 1: Boot into Safe Mode

Safe Mode loads Windows with minimal drivers, allowing diagnosis without interference.

**How to do it:**

1. Restart PC
2. Hold **Shift + Restart**
3. Navigate to:

   * Troubleshoot → Advanced Options → Startup Settings
4. Press **F4 (Safe Mode)**

📌 *Image suggestion: Safe Mode menu screen*

---

### Step 2: Check system logs

Open Event Viewer:

```
eventvwr.msc
```

Go to:

* Windows Logs → System

Look for:

* Critical errors (red icons)
* “CRITICAL_PROCESS_DIED”

📌 This identifies what service failed.

---

### Step 3: Repair system files

Run Command Prompt as Administrator:

```
sfc /scannow
```

### What this does:

* Scans all protected system files
* Replaces corrupted or missing Windows files automatically

Then run:

```
DISM /Online /Cleanup-Image /RestoreHealth
```

### What this does:

* Repairs Windows image itself (deeper repair than SFC)
* Fixes corrupted update or system components

---

## Outcome

System files repaired, system stability restored.

---

## Learning

Most critical process failures are caused by:

* Corrupted Windows updates
* Failed system file updates
* Malware interference

---
