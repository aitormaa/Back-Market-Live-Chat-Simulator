Back Market - Live Chat Training Simulator
An interactive browser-based training tool for Back Market customer service agents. Two users connect to the same live chat session. One user plays the Agent and the other plays the Customer.
Live demo:
https://aitormaa.github.io/Back-Market-Live-Chat-Simulator/
Features
Two-user live sessions

    The Agent creates a six-character session code.
    The Customer joins with that code.
    Firebase Realtime Database synchronizes sessions and messages across devices.
    Firebase Anonymous Authentication protects database access without requiring user accounts.
    PeerJS WebRTC remains available as an optional fast connection method.
    The app continues to work when WebRTC is blocked by a company network because Firebase handles synchronization.

52 training scenarios
Level	Code range	Scenarios	Focus
Beginner	B01-B11	11	Order tracking, invoices, cancellations, warranties, and return policy
Intermediate	I01-I30	30	Device defects, warranty claims, refund disputes, and technical troubleshooting
Advanced	A01-A11	11	Fraud accusations, GDPR issues, hostile customers, legal threats, and vulnerable users
Each scenario includes a customer persona, device and order details, an opening message, a resolution path, customer hints, and key challenges.
Performance timers

    FRT: First Response Time, with a 60-second target.
    NRT: Next Response Time, with a 90-second target.
    Session timer: total session duration.

Agent tools

    Reply and internal note modes.
    Macro library for common responses.
    File and image attachments with client-side image compression.
    Ticket fields for status, priority, and category.
    QA scoring and empathy analysis.

Getting started
Use the live version
Open the live demo in two browser windows or on two devices:
https://aitormaa.github.io/Back-Market-Live-Chat-Simulator/
The Agent and Customer can use different devices and networks. Both users must use the same language version. The English version uses the sessions/en Firebase path.
Run locally
This is a browser application with no build step:

git clone https://github.com/aitormaa/Back-Market-Live-Chat-Simulator.git
cd Back-Market-Live-Chat-Simulator
python3 -m http.server 8080

Open this URL in your browser:
http://localhost:8080
Using a local server is more reliable than opening the file directly with the file:// protocol.
Run a session
Agent

    Open the simulator.
    Choose the Agent role.
    Select a difficulty.
    Enter the Agent name.
    Share the generated six-character session code.
    Wait for the Customer to join.

Customer

    Open the simulator on another browser or device.
    Choose the Customer role.
    Enter the session code from the Agent.
    Start the conversation.

Repository files

Back-Market-Live-Chat-Simulator/
├── index.html
├── firebase-config.js
└── README.md

The English repository uses index.html. The German, Spanish, and French repositories use their own translated index.html files. Each repository also contains its own copy of firebase-config.js.
Firebase configuration
The simulator loads its Firebase Web App configuration from:

firebase-config.js

The file must be in the same repository folder as index.html and must contain:

window.BM_FIREBASE_CONFIG={
  apiKey:'YOUR_API_KEY',
  authDomain:'YOUR_PROJECT.firebaseapp.com',
  databaseURL:'YOUR_DATABASE_URL',
  projectId:'YOUR_PROJECT_ID',
  storageBucket:'YOUR_STORAGE_BUCKET',
  messagingSenderId:'YOUR_MESSAGING_SENDER_ID',
  appId:'YOUR_APP_ID'
};

Firebase web configuration is included in browser applications. Database Rules and Anonymous Authentication protect access to the data.
Required Firebase settings:

    Anonymous Authentication enabled.
    Realtime Database enabled.
    GitHub Pages domain added to Firebase Authentication authorized domains.
    Realtime Database rules published.

English sessions use this path:

Rules
Sessions
$locale
$code
.read
auth != null
.write
auth != null
.validate
newData.hasChildren(['scenario', 'closed'])

The other language repositories use sessions/de, sessions/es, and sessions/fr.
Firebase database rules
Publish these rules in Realtime Database:

Rules
Sessions
$locale
$code
.read
auth != null
.write
auth != null
.validate
newData.hasChildren(['scenario', 'closed'])

Do not require messages in the validation rule. A new session starts with an empty messages object, which may not be stored as a child until the first message is sent.
Technical architecture

Agent browser                    Customer browser
     |                                  |
     |        Firebase Realtime        |
     +------ Database and Auth --------+
     |                                  |
     +------ PeerJS WebRTC, optional ---+

Firebase is the source of truth for sessions and messages. The browser stores a local cache to reduce repeated reads. PeerJS can provide a faster direct connection when the network allows it.
Main application data
The main data arrays are defined in index.html:
Constant	Description
SC	Training scenarios
MACROS	Agent response macros
AUTO	Automated inactivity messages
PCFG	PeerJS connection settings
Troubleshooting
If the browser console says:

Firebase is not configured. Local-only mode is active.

Check that firebase-config.js is in the repository root and that index.html loads it with:

<script src="./firebase-config.js"></script>

If the console says:

PERMISSION_DENIED

Check that Anonymous Authentication is enabled and that the Realtime Database rules have been published.
If the console says:

WebRTC: ICE failed

PeerJS could not create a direct connection. Firebase should still synchronize the session and messages.
Roadmap

    Combine the four language repositories into one multilingual repository.
    Add an administrator dashboard with team analytics.
    Add exportable PDF debrief reports.
    Move large attachments to Firebase Storage instead of storing them in Realtime Database.

Contributors
Built by the Back Market Learning and Development and Customer Care teams.
License
Internal use only. Back Market proprietary tool. Not for public distribution.
â””â”€â”€ index.html        # Entire application - self-contained, no dependencies to install
```

The app uses CDN-loaded libraries (no npm required):
- [React 18](https://react.dev/) - UI framework
- [Tailwind CSS](https://tailwindcss.com/) - styling
- [Babel Standalone](https://babeljs.io/) - in-browser JSX transpilation
- [PeerJS 1.5](https://peerjs.com/) - WebRTC peer-to-peer connection

---

## Architecture

```
AGENT (Tab / Device A)               CUSTOMER (Tab / Device B)

Creates session code                 Joins via session code
Receives scenario + hints            Receives scenario (no hints)
Can see QA scoring                   Sees customer-facing chat only

          |                                    |
          +------------ PeerJS WebRTC ---------+
                BroadcastChannel + localStorage (fallback)
```

**Session data flow:**
1. Agent creates session - scenario stored in `localStorage` under session code
2. PeerJS sends `INIT` payload to Customer on connection
3. Messages broadcast via WebRTC DataChannel + `localStorage` write
4. `BroadcastChannel` keeps same-device tabs in sync (400ms polling as fallback)

---

## Configuration

All scenarios, macros, and auto-messages are defined as plain JavaScript arrays at the top of `index.html` - no backend or database required.

| Constant | Description |
|---|---|
| `SC` | Array of all 52 training scenarios |
| `MACROS` | Agent macro templates (supports `{CUSTOMER_FNAME}`, `{CURRENT_USER_FNAME}`) |
| `AUTO` | Automated messages sent on agent inactivity at 60s and 120s |
| `PCFG` | PeerJS config (STUN servers) |

---

## Scoring Details

### Empathy Score (auto-calculated, max 25 pts)
Analyses all agent messages for:
- Empathy keywords (`understand`, `sorry`, `apologize`, `happy to help`, etc.)
- Use of the **customer's first name**
- Frequency and consistency across the conversation

### QA Score (manual checklist)
Evaluators check items across categories including:
- Greeting quality
- Issue identification
- Solution correctness
- Policy adherence
- Communication clarity
- Response speed (FRT and NRT targets)

---

## Roadmap

- [ ] Firebase / Supabase backend for persistent session history
- [ ] Admin dashboard with team-level analytics
- [ ] Spanish language version (`index-es.html`)
- [ ] Trainer-controlled scenario injection mid-session
- [ ] Exportable PDF debrief report

---

## Contributors

Built by the Back Market Learning & Development / Customer Care team.

---

## License

Internal use only - Back Market proprietary tool. Not for public distribution.
