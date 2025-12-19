================================================================================
                        JINKUSU STARKILLER
                    How This Software Works
================================================================================

OVERVIEW
--------
Jinkusu StarKiller is an advanced phishing platform that creates real-time 
browser sessions to capture login credentials and session cookies from targets.
Unlike traditional phishing pages (static HTML copies), this tool provides 
victims with a REAL browsing experience while secretly recording everything.


HOW IT WORKS - STEP BY STEP
---------------------------

1. ATTACKER SETS TARGET
   - Attacker runs: ./start.sh https://accounts.google.com
   - The platform starts and generates a phishing link

2. VICTIM CLICKS LINK
   - Victim receives link (via email, SMS, social engineering)
   - When clicked, a new container is created just for that victim
   - Each victim gets their own isolated browser session

3. REAL BROWSER SESSION 
   - The victim sees a REAL Chrome browser running on the server
   - This browser is streamed to their screen
   - The browser automatically opens the target login page (e.g., Google)
   - Victim interacts with the REAL website, not a fake copy

4. CREDENTIAL CAPTURE (Keylogger)
   - Every keystroke the victim types is recorded
   - Advanced pattern detection identifies:
     * Username/Email (detected by email patterns)
     * Password (detected by field order and typing pauses)
     * 2FA codes, PINs, security codes
   - Credentials are saved to credentials.json

5. COOKIE CAPTURE
   - After victim logs in successfully, their session cookies are extracted
   - Cookies are decrypted from Chrome's encrypted storage
   - Saved in JSON format (compatible with browser extensions)
   - These cookies can be imported to hijack the session

6. ADMIN PANEL MONITORING
   - Attacker monitors all active sessions in real-time
   - Can see: active victims, captured credentials, cookies, keylogs
   - Can download all captured data
   - Can terminate sessions


TECHNICAL COMPONENTS
--------------------

┌─────────────────────────────────────────────────────────────────────────────┐
│                              ARCHITECTURE                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│   VICTIM'S BROWSER                        OUR SERVER                        │
│   ┌─────────────┐                        ┌─────────────────────────────┐   │
│   │             │   WebSocket/HTTPS      │   (Reverse Proxy)           │   │
│   │   Clicks    │ ───────────────────►   │  - Routes traffic           │   │
│   │   Link      │                        │  - SSL termination          │   │
│   │             │                        └──────────┬──────────────────┘   │
│   └─────────────┘                                   │                       │
│                                                     ▼                       │
│                                          ┌─────────────────────────────┐   │
│                                          │  starkiller Container       │   │
│                                          │  - Creates new sessions     │   │
│                                          │  - Manages  containers      │   │
│                                          └──────────┬──────────────────┘   │
│                                                     │                       │
│                                                     ▼                       │
│                                          ┌─────────────────────────────┐   │
│                                          │  starkiller Container       │   │
│                                          │  (One per victim)           │   │
│                                          │                             │   │
│                                          │  ┌───────────────────────┐ │   │
│                                          │  │ Xvfb (Virtual Display)│ │   │
│                                          │  └───────────────────────┘ │   │
│                                          │            │               │   │
│                                          │  ┌─────────▼─────────────┐ │   │
│                                          │  │ Chrome Browser        │ │   │
│                                          │  │ (Opens target site)   │ │   │
│                                          │  └───────────────────────┘ │   │
│                                          │            │               │   │
│                                          │  ┌─────────▼─────────────┐ │   │
│                                          │  │ Keylogger (Python)    │ │   │
│                                          │  │ - Records keystrokes  │ │   │
│                                          │  │ - Detects credentials │ │   │
│                                          │  └───────────────────────┘ │   │
│                                          │            │               │   │
│                                          │  ┌─────────▼─────────────┐ │   │
│                                          │  │ Cookie Extractor      │ │   │
│                                          │  │ - Extracts cookies    │ │   │
│                                          │  │ - Decrypts values     │ │   │
│                                          │  └───────────────────────┘ │   │
│                                          │            │               │   │
│                                          │  ┌─────────▼─────────────┐ │   │
│                                          │  │ JSK Server            │ │   │
│                                          │  │ - Streams to victim   │ │   │
│                                          │  └───────────────────────┘ │   │
│                                          └─────────────────────────────┘   │
│                                                                             │
│                                          ┌─────────────────────────────┐   │
│   ATTACKER'S BROWSER                     │  Admin Panel (Node.js)     │   │
│   ┌─────────────┐                        │  - Real-time monitoring    │   │
│   │             │   HTTPS                │  - View credentials        │   │
│   │   Admin     │ ◄───────────────────   │  - Download cookies        │   │
│   │   Panel     │                        │  - Manage sessions         │   │
│   │             │                        └─────────────────────────────┘   │
│   └─────────────┘                                                          │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘


KEY FEATURES
------------

1. REAL BROWSER SESSION
   - Not a static phishing page
   - Victim interacts with actual website
   - Bypasses most phishing detection
   - Works with 2FA, CAPTCHA, etc.

2. ADVANCED KEYLOGGER
   - Captures all keystrokes
   - Smart credential detection:
     * Email pattern recognition
     * Typing pause detection (click-based field switching)
     * TAB/ENTER key detection
   - Categorizes: username, password, 2FA codes, PINs

3. SESSION COOKIE THEFT
   - Extracts cookies after successful login
   - Decrypts Chrome's encrypted cookie storage
   - JSON format compatible with EditThisCookie extension
   - Allows session hijacking without password

4. MULTI-TARGET SUPPORT
   - Each victim gets isolated container
   - Multiple simultaneous sessions
   - Automatic resource management

5. ADMIN PANEL
   - Real-time session monitoring
   - Credential viewing and download
   - Cookie export
   - Session termination
   - Hacker-themed UI


CAPTURED DATA STRUCTURE
-----------------------

Downloads/{session-id}/
├── credentials.json        # Parsed credentials (username, password, 2FA)
├── credentials_summary.txt # Human-readable summary
├── cookies.json           # Browser cookies (JSON format)
├── Cookies.txt            # Browser cookies (text format)
├── keylogs.txt            # Raw keystroke log
├── form_captures.json     # Form submission data
└── Default/               # Chrome profile data


EXAMPLE CAPTURED CREDENTIALS
----------------------------

credentials.json:
{
  "captured_at": "2025-12-19 16:04:58",
  "username": "victim@gmail.com",
  "password": "SecretPassword123!",
  "extra_tokens": [
    {
      "type": "2FA_CODE",
      "value": "482910",
      "captured_at": "2025-12-19 16:05:15"
    }
  ]
}


EXAMPLE CAPTURED COOKIES
------------------------

cookies.json:
[
  {
    "domain": "accounts.google.com",
    "name": "SID",
    "value": "g.a000pQh...[session token]...",
    "secure": true,
    "httpOnly": true
  },
  {
    "domain": ".google.com",
    "name": "HSID",
    "value": "AeQS...[auth token]...",
    "secure": true
  }
]


WHY THIS WORKS
--------------

Traditional phishing:
- Fake login page → Victim enters credentials → Page looks suspicious
- Easy to detect (URL mismatch, certificate warnings, missing elements)
- Doesn't capture 2FA codes
- Doesn't steal session cookies

Jinkusu StarKiller:
- Real website → Victim sees legitimate site → No suspicion
- URL is attacker's domain but content is real
- Captures everything: credentials, 2FA, session cookies
- Victim completes real login → Attacker gets valid session


ATTACK FLOW EXAMPLE
-------------------

1. Attacker: ./start-platform.sh https://accounts.google.com

2. Attacker sends link: https://attacker-domain.com/abc123

3. Victim clicks link → Sees real Google login page

4. Victim types: victim@gmail.com [TAB] MyPassword123 [ENTER]

5. Keylogger captures:
   - Username: victim@gmail.com
   - Password: MyPassword123

6. Google asks for 2FA → Victim enters: 482910

7. Keylogger captures:
   - 2FA Code: 482910

8. Login successful → Cookie extractor captures session cookies

9. Attacker can now:
   - Use captured password for other services
   - Import cookies to hijack Google session
   - Access victim's Gmail, Drive, etc.


LEGAL DISCLAIMER
----------------

This software is intended for:
- Authorized penetration testing
- Security research
- Educational purposes

Unauthorized use against systems you don't own or have explicit 
permission to test is ILLEGAL and may result in criminal prosecution.


