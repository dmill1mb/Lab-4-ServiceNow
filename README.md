# Lab 4 — ServiceNow ITSM

**Personal Developer Instance (Free) · No Credit Card · ITSM · ITIL**

| Field | Value |
|---|---|
| Certification alignment | CompTIA A+ · Network+ · ITIL 4 Foundation |
| Free tools | ServiceNow Personal Developer Instance — free at developer.servicenow.com, no credit card, no expiration |
| Time to complete | 2–3 hours across multiple sessions |
| Estimated cost | $0 — ServiceNow PDI is permanently free |
| Career relevance | IT Support · Help Desk · Sysadmin · ITSM Platform Administrator |

---

## Why this lab matters

When users report IT problems, those problems need to be tracked, routed, prioritised, assigned, worked, and resolved — consistently and auditably. Without a structured system, things fall through the cracks. A server goes down, three people report it to three different team members, nobody knows which one is working it, and a one-hour fix turns into a four-hour outage.

ServiceNow is how most enterprise IT organisations solve this. It is an IT Service Management platform — a structured system for handling every type of IT request, from a password reset to a major infrastructure outage. It enforces process: incidents follow an incident workflow, change requests require approval, service requests come from a catalogue with predefined fulfilment steps.

ServiceNow is one of the most widely deployed enterprise software platforms in the world. If you are going into IT support, you will use it — or something that works exactly like it — from your first week on the job. Having hands-on experience before you start is a meaningful differentiator.

| Role | How this lab applies |
|---|---|
| IT Support / Help Desk | Creating and resolving incidents is the core daily task of every help desk role |
| Sysadmin | Change management — logging, approving, and documenting infrastructure changes before they happen |
| IT Service Manager | Building service catalogues, defining workflows, reporting on SLA compliance |
| Cloud Engineer | Cloud operations teams use ServiceNow for change requests, incident management, and service requests for cloud resources |

## What you'll learn

| Skill | Real-world application |
|---|---|
| Create and resolve an incident | The most common task in every IT support role — done from day one |
| Set ticket priority and SLA | Priority determines response time; SLAs define the commitment to the business |
| Assign tickets to queues and individuals | Routing is critical — the wrong person working a ticket wastes time and delays resolution |
| Build a service catalogue item | Lets users self-serve common requests without calling the help desk for every ticket |
| Create a workflow for approvals | Change and access requests require manager approval — workflows automate this |
| Run reports on ticket volume and resolution time | Metrics drive IT operations decisions at every level |
| Understand ITIL incident vs problem vs change | The three core ITIL process types — every enterprise IT role uses this language |

---

## Architecture — how the platform is structured

```
                                        ┌───────────────────────────┐
                                        │ LOCAL MACHINE             │
                                        │ ─────────────             │
                                        │ Admin browser             │
                                        │   Chrome / Edge / Firefox │
                                        │   No install required     │
                                        └───────────────────────────┘
                                                      │
                                                      │  HTTPS / port 443
                                                      ▼
┌───────────────────────────────────────────────────────────────────────────────────────────────────────────┐
│  ServiceNow Personal Developer Instance  (dev[xxxxx].service-now.com)                                     │
│                                                                                                           │
│  ITSM Modules                                                                                             │
│  ────────────                                                                                             │
│   ┌───────────────────────────┐   ┌───────────────────────────────┐   ┌─────────────────────────────┐    │
│   │ Incident Mgmt             │   │ Change Mgmt                   │   │ Service Catalog             │    │
│   │ ─────────────             │   │ ───────────                   │   │ ───────────────             │    │
│   │ Create / assign / resolve │   │ Standard / Normal / Emergency │   │ Self-service request portal │    │
│   │ Work notes · SLA tracking │   │ Approval workflow             │   │ Variables · Fulfilment      │    │
│   └───────────────────────────┘   └───────────────────────────────┘   └─────────────────────────────┘    │
│                                                                                                           │
│      ┌─────────────────────────────┐   ┌─────────────────────────┐   ┌──────────────────────────┐        │
│      │ Problem Mgmt                │   │ Flow Designer           │   │ Reports                  │        │
│      │ ────────────                │   │ ─────────────           │   │ ───────                  │        │
│      │ Root cause analysis         │   │ Visual workflow builder │   │ Bar / pie / list charts  │        │
│      │ Links incidents to problems │   │ Approval automation     │   │ Priority · MTTR · Volume │        │
│      └─────────────────────────────┘   └─────────────────────────┘   └──────────────────────────┘        │
│                                                                                                           │
└───────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

Unlike the previous labs in this series, ServiceNow runs entirely in the cloud as a SaaS product. There is nothing to install locally — your browser connects to your Personal Developer Instance over HTTPS, and the entire platform (database, application server, workflow engine) runs on ServiceNow's infrastructure. Your PDI URL is unique to your account and persists between sessions as long as you keep it active.

---

## Key concepts — read this before starting

**What is ITSM?** IT Service Management is the set of processes and tools an organisation uses to design, deliver, manage, and improve IT services. ITSM isn't just a ticketing system — it's a framework that defines how incidents are handled, how changes are controlled, how requests are fulfilled, and how performance is measured. ServiceNow is the software platform that implements that framework.

**What is ITIL?** ITIL (IT Infrastructure Library) is the most widely adopted ITSM framework. It defines best practices for IT service management and provides a common vocabulary. The ITIL 4 Foundation certification tests your understanding of this vocabulary and these processes. This lab gives you hands-on context for concepts that are otherwise abstract on a study guide.

**What is a Personal Developer Instance (PDI)?** A full, free copy of the ServiceNow platform provisioned specifically for learning and development. It includes all core modules — Incident, Change, Problem, Service Catalog, Flow Designer, Reports — and behaves identically to the enterprise version organisations pay for. The only difference is scale: a PDI is a single-tenant instance with sample data, not connected to any real organisation's systems.

**ITIL process types you need to know:**

| ITIL Term | Definition | ServiceNow Module |
|---|---|---|
| Incident | An unplanned interruption to a service. Goal: restore service as quickly as possible | Service Desk → Incidents |
| Problem | The root cause of one or more incidents. Goal: eliminate the root cause permanently | Service Desk → Problems |
| Change | A planned modification to infrastructure or applications. Goal: implement with minimal risk | Change → Changes |
| Service Request | A user request for something new — access, hardware, information. Not a break/fix | Service Catalog |
| SLA | Service Level Agreement — the committed response and resolution time for each priority level | SLA → SLA Definitions |
| CMDB | Configuration Management Database — the record of every IT asset and its relationships | Configuration → CIs |
| Knowledge Base | Articles documenting known issues and solutions — reduces repeat incident volume | Knowledge → Articles |

**What is a work note vs a comment?** Work notes are internal — visible to IT staff only, used to document investigation steps, decisions, and internal communication. Comments (also called customer-visible notes) are external — visible to the person who raised the ticket. Always use work notes for internal investigation activity and comments only when you intend the user to see the update.

**What is ticket priority?** Priority is calculated from two inputs: impact (how many users or services are affected) and urgency (how time-sensitive is the situation). Most organisations use a P1–P4 or 1–4 scale:

| Priority | Meaning | Typical response target |
|---|---|---|
| 1 — Critical | Major service down, many users affected | 15 minutes |
| 2 — High | Significant impact, workaround not available | 1 hour |
| 3 — Moderate | One user affected, workaround available | 4 hours |
| 4 — Low | Minor issue, no business impact | Next business day |

---

## Step 1 — Get your free instance

1. Go to `developer.servicenow.com`
2. Click **Sign Up** — email and password only, no credit card required
3. Once logged in, click **Request Instance**
4. Select the latest stable release (Washington or newer)
5. Click **Request** — your instance provisions in 10–15 minutes
6. You will receive an email with your instance URL (`dev[xxxxx].service-now.com`) and login credentials

> **Keep your instance active.** ServiceNow hibernates PDIs that haven't been accessed in 10 days and reclaims instances inactive for more than 30 days. Log in at least once a week. If an instance is reclaimed, you can request a new one for free — but you lose your work.

---

## Step 2 — Navigate the platform

When you first log in you are in the ServiceNow admin interface. The left navigation panel gives you access to all modules.

| Module | Where it is | What it does |
|---|---|---|
| Incident | Service Desk → Incidents | The primary module for IT support tickets |
| Problem | Service Desk → Problems | Root cause analysis for recurring incidents |
| Change | Change → Changes | Planned modifications to IT infrastructure |
| Service Catalog | Service Catalog → Catalogs | The user-facing request portal |
| Reports | Reports → Create New | Analytics and metrics |
| Flow Designer | Process Automation → Flow Designer | Visual workflow builder |

Take five minutes to click through each of these before starting Step 3. Understanding where things live in the navigation removes friction when you are following the steps below.

---

## Step 3 — Create and work an incident

### Create the incident

1. Navigate to **Service Desk → Incidents → New**
2. Fill in the form using the scenario below

| Field | Value |
|---|---|
| Caller | Search for and select **Abel Tuter** (a built-in test user) |
| Category | Software |
| Subcategory | Email |
| Short description | `User cannot access Outlook — error: Cannot connect to server` |
| Description | `User reports that Outlook stopped working this morning at approximately 9am. Error message: 'Cannot connect to the Exchange server. Verify your network settings.' Other users in the same building are not affected. User is on a laptop, connected via Wi-Fi.` |
| Priority | 3 — Moderate (one user affected, workaround available — webmail) |
| Assignment Group | Service Desk |

3. Click **Submit**
4. Note the ticket number (format: `INC0001234`) — this is the tracking ID for every subsequent action
<img width="1910" height="494" alt="image" src="https://github.com/user-attachments/assets/14d77ec7-76ba-4758-b3fb-a0e73e336531" />

### Work the incident

1. Open the incident you just created
2. Change **State** to `In Progress`
3. Set **Assigned to** to yourself — click the field and search your username
4. <img width="1889" height="453" alt="image" src="https://github.com/user-attachments/assets/0c710234-7996-4fa2-a0d0-13efd44042d1" />

5. Add a **Work Note** (visible to IT staff only, not the caller):

```
Contacted user. Confirmed error message. Outlook profile appears corrupted.
Attempting profile repair. Instructed user to use OWA (webmail) in the interim.
Resolution ETA: 30 minutes.
```
<img width="1597" height="491" alt="image" src="https://github.com/user-attachments/assets/b6459b27-0135-4a3e-9d69-50fc1fb36cad" />

5. Add a resolution in the **Resolution Notes** field:

```
Rebuilt Outlook profile. Removed and re-added the Exchange account.
User confirmed Outlook is working. Issue was a corrupted OST file.
Closed with user confirmation.
```
<img width="1604" height="214" alt="image" src="https://github.com/user-attachments/assets/8168779a-7e56-43b1-90fd-f40ca3f30c67" />


6. Change **State** to `Resolved`, then `Closed`
<img width="1902" height="474" alt="image" src="https://github.com/user-attachments/assets/edf16519-9e13-4e88-8aac-9e467f7cd435" />

**What you just did:** you followed the full ITIL incident lifecycle — detection, logging, categorization, prioritization, assignment, investigation, resolution, and closure. Every IT support role performs this sequence dozens of times a day.

---

## Step 4 — Build a service catalogue item

Service catalogue items let users request common IT services through a self-service portal without calling the help desk. This reduces ticket volume for routine requests and frees up IT staff for work that requires human judgement.

### Create a new laptop request item

1. Navigate to **Service Catalog → Catalogs → Service Catalog**
2. Click **Maintain Items → New**
3. Fill in the item details:

| Field | Value |
|---|---|
| Name | New Laptop Request |
| Category | Hardware |
| Short description | `Request a new or replacement laptop` |
| Description | `Use this form to request a new laptop for a new hire or to replace a failed or end-of-life device. Requests are reviewed within 2 business days. Delivery takes 5–7 business days after approval.` |
| Fulfillment group | IT Hardware Team |
| Price | Leave blank — internal requests do not have a user-facing cost |
<img width="1769" height="737" alt="image" src="https://github.com/user-attachments/assets/937514d2-0a76-4fa4-ad66-a267ff8a6f75" />

4. Click **Submit** to save
5. Click the **Variables** tab and add the following fields:

| Variable Name | Type | Mandatory |
|---|---|---|
| Requester Name | Single Line Text | Yes |
<img width="1868" height="657" alt="image" src="https://github.com/user-attachments/assets/e506b091-f0be-425c-8fe0-1e748a8a1018" />

| Business Justification | Multi Line Text | Yes |
<img width="1819" height="613" alt="image" src="https://github.com/user-attachments/assets/0b60d2ff-e85e-4b51-91bc-f2f33a78536b" />

| Required By Date | Date | Yes |
<img width="1802" height="630" alt="image" src="https://github.com/user-attachments/assets/0f697d5d-d68f-43c6-a51d-a93207be3dc4" />

| Laptop Model Preference | Select Box (Options: Standard / Developer / Executive) | No |
<img width="1896" height="818" alt="image" src="https://github.com/user-attachments/assets/b61a71e7-33e4-402a-87bf-00bca34dd558" />


6. Save and click **Preview** — the item now appears in the service catalogue portal as a user would see it
<img width="1903" height="855" alt="image" src="https://github.com/user-attachments/assets/514cf782-56d1-48e2-8eb2-c891e74fbddd" />

**Why this matters:** every self-service request that comes through the catalogue instead of a phone call or email is a measurable reduction in unstructured work. Catalogue items also enforce data collection — the variables you defined mean every request arrives with the information needed to fulfil it, rather than requiring the IT team to chase the requester for details.

---

## Step 5 — Create a change request with an approval workflow

Change requests require manager approval before work begins. This is a core ITIL control — changes to production infrastructure need authorisation to prevent uncoordinated modifications that could cause outages.

### Create the change request

1. Navigate to **Change → Changes → New (Standard)**
2. Fill in the request:

| Field | Value |
|---|---|
| Short description | `Deploy security patch MS24-001 to all Windows workstations` |
| Category | Software |
| Risk | Low |
| Impact | 2 — Medium |
| Start date | Next Saturday at 2:00 AM |
| End date | Next Saturday at 6:00 AM |
| Description | `Monthly security patch deployment. Patch addresses CVE-2024-0001 rated CVSS 7.8. Workstations will require one reboot. Deployed via WSUS. Rollback plan: uninstall via WSUS if issues reported post-deployment.` |

3. Under the **Planning** tab, add a Test Plan and a Backout Plan
4. Click **Request Approval** — this moves the change to **Pending Approval** state
5. Navigate back to the change, find the **Approvals** tab, and approve it as the admin user
6. The change moves to **Scheduled** state

**Why the approval workflow matters:** in an enterprise environment, an unapproved change to a production system can trigger a major incident, fail a compliance audit, or violate a change freeze during a critical business period. The approval workflow creates a documented trail — who requested the change, what the risk assessment was, who approved it, and when. That trail is what auditors look for and what incident responders rely on when something goes wrong after a deployment.

---

## Step 6 — Build reports

Reports give IT management visibility into operational performance. Without them, you are managing by anecdote — someone's impression of how busy the team is, rather than data showing exactly where capacity is going and where service commitments are being missed.

### Incident volume by priority

1. Navigate to **Reports → Create New**
2. Fill in:
   - **Name:** `Incident Volume by Priority — Last 30 Days`
   - **Data:** Incident [incident]
   - **Type:** Bar Chart
   - **Group by:** Priority
   - **Condition:** Created is on or after `30 days ago`
3. Click **Save and Run**

### Build two more reports for your portfolio

**Mean Time to Resolution (MTTR) by assignment group** — bar chart grouped by Assignment Group. This shows which teams are resolving tickets fastest and which have a backlog problem. In a real environment, MTTR by team feeds staffing decisions and training investments.

**Open incidents by assigned agent** — useful for workload balancing. If one agent has 40 open tickets and another has 5, a manager needs to see that before the first agent burns out or SLAs start breaching.

---

## Verification — confirm the lab is working

| Check | How to verify |
|---|---|
| Incident lifecycle complete | Open your incident — State shows `Closed`, Work Notes show your internal note, Resolution Notes field is populated |
| Correct priority applied | Incident shows Priority 3 — Moderate; confirm the impact/urgency combination that produced it is documented in your notes |
| Ticket assigned correctly | Assigned to field shows your username; Assignment Group shows `Service Desk` |
| Catalogue item live | Navigate to the Service Catalog portal as a non-admin user and confirm your New Laptop Request item appears and the variables render correctly |
| Change approved and scheduled | Open your change request — State shows `Scheduled`, Approvals tab shows your approval with timestamp |
| Reports returning data | All three reports show populated charts; confirm the date filter on the priority report is working by checking a few of the incidents it includes |

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Instance URL not working after provisioning | Wait the full 10–15 minutes — the email sometimes arrives before the instance is ready. If it still fails after 20 minutes, log into `developer.servicenow.com` and check the instance status dashboard |
| Instance is in hibernation | Log into `developer.servicenow.com`, click **Wake Instance**, and wait 2–3 minutes for it to come back online |
| Can't find a module in the left navigation | Use the search bar at the top of the navigation panel — type the module name. Alternatively, use the global search (`All` at the top of the left nav) |
| Abel Tuter doesn't appear in the Caller field | The sample user may be named slightly differently in your instance version. Search for "Abel" and select whichever test user appears, or create a new user under **User Administration → Users → New** |
| Catalogue item not appearing in the portal | Confirm the item's **Active** checkbox is checked and it is associated with a published catalogue. Navigate to the portal at `dev[xxxxx].service-now.com/sp` to view it as an end user |
| Change request stuck in Pending Approval | The approval needs to be actioned — navigate to the Approvals tab on the change record and approve it manually as the admin user |
| Report returns no data | Confirm the date filter is set correctly and that you have created at least one incident within the time range. Widen the filter to `All time` to rule out a date issue |

---

## Documentation checklist

Before you close this lab out, capture it for your portfolio:

- [ ] Screenshot of a completed incident showing State: Closed, work notes, and resolution notes
- [ ] Screenshot of the New Laptop Request catalogue item as it appears in the portal
- [ ] Screenshot of the change request in Scheduled state with the Approvals tab showing approval
- [ ] Screenshot of at least one report with populated data
- [ ] One paragraph per screenshot explaining what process it supports and why it matters in an IT environment

---

## Reflection

**What clicked into place**

The approval workflow on the change request was the moment this lab stopped feeling like a form-filling exercise and started feeling like an actual system.

I filled in the change request — patch deployment, risk level, start and end time, test plan, rollback plan — clicked Request Approval, and watched the state change to Pending Approval. Then I navigated to the Approvals tab, approved it as the admin user, and the change moved to Scheduled. The whole sequence took about two minutes. But what it represented took a moment to land: no one in that environment can touch a production system on Saturday morning without that paper trail existing first. The request had to be written up, the risk had to be assessed, someone with authority had to review it and sign off, and only then does the work have a green light.

Before this lab, I understood change management as a concept. After working through it in ServiceNow, I understand it as a control — a mechanism that prevents the specific failure mode where a well-intentioned engineer makes a modification to a production system on a Friday afternoon, something breaks over the weekend, and nobody can reconstruct what changed or why because nothing was documented before the work started. The approval workflow exists precisely to eliminate that scenario.

**What I'd do differently at enterprise scale**

The change I built here was a Standard change — low risk, pre-approved category, straightforward approval from one person. In a real enterprise environment, the change management process is considerably more involved.

Normal changes — anything with significant risk or impact — go through a Change Advisory Board (CAB). The CAB is a weekly meeting where change requests are reviewed by representatives from IT operations, security, and the business. A patch deployment touching every workstation in a company would likely be a CAB item, not a one-click approval. The requester would present the change, answer questions about the rollback plan, confirm the testing approach, and the CAB would vote to approve, defer, or reject. The ServiceNow workflow would reflect that — multiple approvers, a required CAB meeting date, a structured review record.

Enterprise environments also maintain change freezes — periods when no changes to production are permitted, typically around major business events like quarter-end financial close, peak retail seasons, or major product launches. A sysadmin who submits a change request without knowing whether the organisation is in a freeze learns about it quickly and not in a pleasant way.

There is also the CMDB relationship that this lab only touched on. In a mature ServiceNow deployment, every change request is linked to the Configuration Items (CIs) it affects — the specific servers, applications, or network devices being modified. When something breaks after a change, the CMDB link is what lets the incident responder look up "what changed on this CI in the last 72 hours" in a single query. Building that relationship correctly from the start is one of the harder ongoing challenges in enterprise ITSM.

**How this maps to the sysadmin role I'm targeting**

Sysadmins sit on both sides of ServiceNow. They work the incidents that come in — the server that's down, the service that stopped, the authentication failure affecting a department. And they submit the changes that go out — the patch deployment, the configuration update, the infrastructure modification that needs to go through CAB before anyone touches production.

Understanding the platform from both sides matters. A sysadmin who has never used ServiceNow before their first enterprise job will spend their first weeks learning the tool while also learning the environment — a slower start than necessary. Having worked through the full ticket lifecycle, built a catalogue item, and navigated the approval workflow means the platform is familiar before day one.

It also means I can speak to change management in interviews in concrete terms rather than abstract ones. Change requests aren't bureaucracy for its own sake — they are how an organisation maintains a known-good state, reduces unplanned outages, and demonstrates to auditors that production modifications are controlled. That framing is the difference between an answer that describes a process and an answer that shows you understand why the process exists.

**Where the four labs connect**

Looking back across all four labs, they form a stack that maps to how an IT environment actually operates.

Lab 1 built the identity layer — Active Directory controls who can log into what and what policies apply to their machines. Lab 2 built the network visibility layer — Wireshark shows what is actually moving across the wire when something goes wrong. Lab 3 built the log aggregation layer — Splunk collects what everything in the environment is doing and makes it searchable. Lab 4 built the process layer — ServiceNow is the system that structures how IT responds when something in those first three layers breaks or needs to change.

A sysadmin operates across all four. When an alert fires in Splunk, they investigate with the identity context from AD and the network context from packet analysis, then document the resolution in ServiceNow and submit a change request if infrastructure needs to be modified to prevent recurrence. The labs were built separately but they describe the same environment.
## 📬 Contact
**Demarcus Miller** —  · [www.linkedin.com/in/demarcus-miller-96502b1a7] · [MillerSDemarcus@gmail.com]
