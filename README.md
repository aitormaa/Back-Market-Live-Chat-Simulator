# Back Market - Live Chat Training Simulator

An interactive, browser-based training tool for Back Market customer service agents. Pairs two users in real time: one plays the **Agent**, one plays the **Customer**, to practice live chat handling across realistic support scenarios.

> **Live demo:** https://aitormaa.github.io/Back-Market-Live-Chat-Simulator/
---

## Features

### Dual-User Multiplayer
- Two participants connect via a shared **Session Code**
- One user selects a difficulty and becomes the **Agent**
- The other joins via code and becomes the **Customer**
- Real-time sync via **PeerJS (WebRTC)** with localStorage + BroadcastChannel fallback
- Works across two browser tabs on the same device or across two different devices

### 52 Training Scenarios - 3 Difficulty Levels

| Level | Code range | Scenarios | Focus |
|---|---|---|---|
| Beginner | B01-B11 | 11 | Order tracking, invoices, cancellations, warranties, return policy |
| Intermediate | I01-I30 | 30 | Device defects, warranty claims, refund disputes, technical troubleshooting |
| Advanced | A01-A11 | 11 | Fraud accusations, GDPR violations, hostile customers, legal threats, vulnerable users |

Each scenario includes:
- Customer **persona** (name, device, order details, mood)
- Opening **customer message**
- **Resolution path** guide (visible to Agent only)
- **Hints** about customer behaviour
- **Key challenges** to practice

### Performance Timers
- **FRT** - First Response Time (target: under 60s), auto-countdown from session start
- **NRT** - Next Response Time (target: under 90s), resets on each customer message
- **Session Timer** - total elapsed time

### Automated Customer Behaviour
- If the agent has not replied after **60 seconds**: automated queue message sent
- If still no reply after **120 seconds**: chat auto-transferred to async

### Agent Tools
- **REPLY / NOTE** toggle (internal notes are not visible to the customer)
- **Macro library** (8 pre-built responses: greeting, hold, escalation, close, recap note, etc.)
- **File and image upload** with client-side image compression (max 600px, JPEG 65%)
- Ticket fields: **Status**, **Priority**, **Category**

### QA Scoring and Empathy Analysis
- Automatic **Empathy Score** calculated from agent messages (keyword detection, use of customer name, frequency)
- Manual **QA checklist** with weighted scoring
- Full session **debrief report** at chat close

---

## Getting Started

### Option 1 - Use the live version
No setup needed. Open https://aitormaa.github.io/Back-Market-Live-Chat-Simulator/ in two browser windows or on two devices.

### Option 2 - Run locally
Since it is a single HTML file with no build step:

```bash
git clone https://github.com/aitormaa/backmarket-chat-simulator.git
cd backmarket-chat-simulator
open index.html
```

> Note: Some browsers restrict localStorage and BroadcastChannel on `file://` URLs. Use a local server for best results:

```bash
npx serve .
# or
python3 -m http.server 8080
```

---

## How to Run a Session

### Agent (Trainer or Trainee)
1. Open the simulator
2. Choose a difficulty: **Beginner**, **Intermediate**, or **Advanced**
3. Enter your name
4. A **6-character Session Code** is generated - share it with your partner
5. Wait for the customer to join, then the chat begins

### Customer (Role-Player)
1. Open the simulator on another tab or device
2. Click **"Join as Customer"**
3. Enter the Session Code shared by the Agent
4. The scenario loads automatically - start chatting

---

## File Structure

```
backmarket-chat-simulator/
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
