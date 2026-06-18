# Copilot Studio Administration Guide for System Admins

**Purpose:** This guide is for System Administrators who manage Microsoft Copilot Studio environments and who need to answer common questions from makers/users. It summarises what **Environment Makers** can do, what **System Administrators** can do, how publishing and sharing work, where agents can be deployed, and how to test and revoke access.

**Last reviewed:** 18 June 2026

---

## 1) Key role model to understand first

### 1.1 Tenant admin roles vs environment roles vs Dataverse roles

In Power Platform, permissions come from **different scopes**. Tenant-level admin roles (for example **Global Administrator**, **Power Platform Administrator**, **Dynamics 365 Administrator**) manage environments and platform settings across the tenant, but they **do not automatically grant Dataverse data access** inside a Dataverse-enabled environment. For Dataverse-backed environments, a user also needs an appropriate **Dataverse security role** in that specific environment, such as **System Administrator**.

Microsoft documents the main role scopes as:

- **Tenant-level admin roles** → manage environments and platform settings across the tenant.

- **Environment-level roles** (for example **Environment Admin**, **Environment Maker**) → manage or create resources in an environment, especially where Dataverse is not the controlling access model.

- **Dataverse security roles** (for example **System Administrator**, **System Customizer**, **Basic User**) → control access to apps, tables, records and security operations inside Dataverse-enabled environments.

### 1.2 Practical meaning for Copilot Studio

For Copilot Studio in a Dataverse-enabled environment, the real separation is usually:

- **Environment Maker** = can create/build resources in the environment, but does **not** automatically get the power to administer Dataverse security.
- **System Administrator** = can administer the environment’s Dataverse security model, assign roles, manage access, and perform full admin actions in that environment.

---

## 2) What Environment Makers can do

Environment Makers are intended for builders, not security administrators. Microsoft describes the role as allowing users to create resources such as apps, flows, and connections in an environment, while following least-privilege principles.

### Environment Makers can generally:

- Create apps, flows, connections and other maker resources in the environment.
- Build and configure Copilot Studio agents
- Test agents in Copilot Studio and publish updated agent content. Publishing is required before end users can interact with the latest version.
- Connect agents to supported channels such as **Teams**, **Microsoft 365 Copilot**, **SharePoint**, **Power Pages**, and other supported channels/clients.
- Share agents with other users for chat or collaboration **subject to environment security and sharing controls**.

### Environment Makers cannot be assumed to do the following:

- They do **not** automatically get Dataverse data/security admin rights.
- They do **not** automatically get permission to assign security roles to other users. Assigning security roles requires appropriate privileges, and Microsoft notes that the assigner needs the necessary security-role privileges; by default, **System Administrator** has these.
- Therefore, an Environment Maker can hit errors such as **`prvAssignRole`** when a sharing action requires role assignment behind the scenes (for example, broader access changes in a Dataverse-backed environment). This aligns with Microsoft’s documented rule that role assignment requires the appropriate privileges and is not a built-in Environment Maker capability.

### Useful admin explanation for users

If a maker asks, “Why can I build and publish but not share org-wide?”, the short answer is:

> Building/publishing is a maker capability; broad sharing in a Dataverse-backed environment can require security-role assignment, which is an admin/security function rather than an Environment Maker capability.

---

## 3) What System Administrators can do

A **System Administrator** in the Dataverse environment is the role with the broadest control over security and resources in that environment. Microsoft states that the System Administrator role has the required privileges to assign security roles to any user, including assigning the System Administrator role itself.

### System Administrators can generally:

- Manage Dataverse security roles and assign roles to users and teams in that environment. 
- Fix access issues that makers cannot fix themselves (for example, missing permission to assign roles/share broadly). 
- Manage users, teams, and security settings in the environment through the **Power Platform admin center**. Microsoft documents user/security-role management under environment settings and security-role management pages.
- Control how broadly agents can be shared by using **Managed Environments** sharing controls. Microsoft states admins can enforce who can grant **Editor** and **Viewer** assignments and can limit or block certain sharing patterns.
- Share agents to larger audiences, including potentially **everyone in the organisation**, subject to authentication/channel requirements and any enforced sharing controls.
- Submit eligible Teams apps/agents into the organisational catalogue for broader discoverability where supported by the Teams/M365 channel and admin approval flows.

### Admin portal reference

The main administration portal for environment management is:

- **Power Platform admin center**: `https://admin.powerplatform.microsoft.com` (commonly referred to as **admin.powerplatform**). Microsoft’s security-role and environment-management documentation routes admins through this portal for environment settings, users, permissions, and security roles.
---

## 4) Publishing vs sharing vs permissions (important distinction)

A lot of confusion comes from mixing up **publishing**, **deploying to a channel**, and **sharing/permissions**. These are related but different.

### 4.1 Publish

Publishing makes the latest agent content available to connected channels. Microsoft notes that after changes are made, the agent must be republished for users to interact with the latest content.

### 4.2 Connect/deploy to a channel

After publishing, the agent can be connected to channels/clients such as **Teams**, **Microsoft 365 Copilot**, **SharePoint**, **Power Pages**, or custom/web clients.

### 4.3 Share / grant access

Sharing controls **who can use** or **collaborate on** the agent. Microsoft documents two main sharing patterns:

- share for **chat/use** (including security groups or the organisation); and
- share for **collaborative authoring**.

### 4.4 Permissions assigned when sharing

Microsoft’s sharing-controls documentation distinguishes **Editor** and **Viewer** style permissions:

- **Editor** = can edit, configure, share, publish, and chat with the agent. Microsoft says Editor permissions **can only be given to individual users**, not security groups. 
- **Viewer** = can only chat with the agent. Microsoft says viewers can be controlled with sharing rules, including sharing to individuals or security groups as Viewers. 

**Practical admin interpretation:**

- A **security group** is a good way to grant **use/chat access** without giving edit rights.
- If someone needs to author/configure/publish the agent, they need to be added individually with an appropriate collaboration role, and they also still need sufficient environment security.

---

## 5) Managing who can use the bot

### 5.1 Add users or security groups

Microsoft documents that an agent can be shared with:

- **Security groups** for chat/use.
- **Everyone in the organization** for chat/use.
- **Individuals** for collaboration or direct access.

### 5.2 Security groups: good for view/use only

For admin guidance, the simplest safe explanation is:

- Add a **security group** when you want a set of users to **use the bot** without individually granting authoring/edit rights. Microsoft explicitly supports sharing with security groups for chat/use, while Editor assignment is limited to individuals.

### 5.3 Individuals: permissions can be changed

For individual users, permissions are more flexible because editors are assigned to individuals, not groups. Microsoft states that **Editor** permissions can only be granted to individual users.

**Practical admin wording:**

- **Individual user** = can be changed between collaborative roles depending on policy and the sharing rules in force.
- **Security group** = use mainly to grant usage/chat access at scale rather than editing/admin rights.

### 5.4 Revoke access

To revoke access, remove the user/group from the agent’s sharing configuration or change the effective permission to no longer allow use/collaboration. Microsoft’s sharing guidance is based around the agent’s share experience, and its sharing controls are enforced when users try to share apps/flows/agents.

**Admin note from testing:** In internal testing, the **sidebar sharing panel opened via the three dots (`...`)** was more reliable for removing users/security groups than the older/main sharing modal. Treat this as an **operational tip from testing**, not as a formal Microsoft-documented requirement.

---

## 6) Testing a published bot via link from Channels

This is the cleanest way for admins to validate a deployment without waiting for users to discover the bot.

### Teams and Microsoft 365 Copilot
Microsoft’s Teams/M365 channel documentation states that after publishing at least once, you can connect the agent to the **Teams and Microsoft 365 Copilot** channels, install it for yourself, and **share the installation link with other users**.

### Recommended admin test process

1. Open the agent in **Copilot Studio**.
2. Select **Channels**.
3. Open **Teams and Microsoft 365 Copilot**.
4. Confirm the agent is connected/published.
5. Use the available installation/open/share link options to open the agent directly in Teams/M365 Copilot for testing. Microsoft explicitly documents that you can install the agent for yourself and share the installation link with others.

### Why this matters

Publishing and sharing do **not** necessarily mean the bot becomes automatically obvious to all users immediately. The install/open/share link is the fastest deterministic test method for admins and pilot users. Microsoft documents the install and link-sharing behaviour in the Teams/M365 channel experience.

### Session/version warning

After publishing updates, some channels with persistent conversations (for example Teams) might continue using the previous conversation session for a period. Microsoft notes that users may need to start a new session (for example by entering **start over**) or wait for the session refresh window for the latest published content to take effect. citeturn8search23

---

## 7) Where agents can be deployed

Microsoft states Copilot Studio can deploy agents to many channels/clients, including **Teams**, **Microsoft 365 Copilot**, **SharePoint**, **Power Pages**, and more. It also supports advanced scenarios such as custom applications via the **Direct Line API**.

### 7.1 Teams

Teams is a native channel. Microsoft documents that admins/makers can:

- install the agent for themselves;
- share the installation link;
- show the agent in the Teams app store;
- submit the agent for admin approval for the **Built for your org** area; and
- add the agent to a **team channel**.

### 7.2 Microsoft 365 Copilot

Microsoft documents that the same Teams + Microsoft 365 channel flow can make the agent available in **Microsoft 365 Copilot** after publishing and configuration. 

### 7.3 SharePoint

Microsoft has dedicated documentation for publishing an agent to **SharePoint**. The documented process is:

- publish the agent first;
- go to **Channels**;
- open the **SharePoint** tile;
- select the site and **Deploy**. Microsoft also notes that this requires **write access to the SharePoint site**, and that usage in SharePoint still follows **Copilot Studio billing/capacity**.

### 7.4 Other channels / clients

Microsoft states other channels and clients are available, including **Power Pages** and more advanced/custom integrations via **Direct Line API**. Additional channel-by-channel governance or implementation research may still be needed before production rollout.

**Recommended wording for internal guide:**
> Teams, Microsoft 365 Copilot and SharePoint are confirmed supported deployment targets. Other channels are available in Copilot Studio, but further research/validation may be required before relying on them in production.

---

## 8) Adding an agent to Teams channels

Microsoft’s Teams/M365 documentation explicitly states that admins/makers can **add the agent to a team channel** as part of the Teams deployment options. 

### Admin notes

- This is a **channel distribution** option, not the same thing as broadening edit rights.
- Users still need the appropriate access/sharing/installation path for successful usage.

---

## 9) Org-wide sharing and the `prvAssignRole` type issue

When a maker tries to share very broadly in a Dataverse-backed environment and receives an error such as **`prvAssignRole is needed`**, the most useful admin interpretation is:

- the action is not just a simple UI share operation;
- it is trying to perform a security-role assignment or equivalent privileged access operation in Dataverse; and
- the user lacks sufficient role-assignment privilege.

Microsoft’s security-role documentation states that assigning security roles requires the correct privileges and that **System Administrator** has all required privileges by default.

**Admin message to users:**
> If you can build the agent but cannot grant broad access, the issue is usually that your maker role allows authoring but not Dataverse security-role assignment. A System Administrator can complete the access change if required.

---

## 10) Governance controls admins should know about

Microsoft now provides **sharing limits** for agents in **Managed Environments**, allowing admins to control how broadly makers can share agents. Reported controls include:

- allow/block granting **Editor** permissions;
- allow/block granting **Viewer** permissions to individuals or security groups;
- block sharing to security groups; and
- limit the number of individuals who can be shared to as viewers. 

Important details Microsoft documents:

- Sharing rules are enforced when users **try to share**.
- Existing access before the rule change is not automatically retroactively removed. 
- Editor permissions can only be given to **individual users**, not security groups.

This is useful if the organisation wants to prevent broad/self-service sharing while still allowing controlled development.

---

## 11) Recommended admin operating model

### Suggested split of responsibilities

**Environment Makers**

- Build and test agents.
- Configure supported channels where permitted.
- Share with approved pilot users/groups within policy.

**System Administrators**

- Manage Dataverse roles, fix permission issues, and handle broad/org-wide access. 
- Enforce sharing rules through Managed Environments if needed.
- Manage production/pilot governance and validate channels. 

### Best-practice admin line to use with users

> Makers can build and publish agents, but broad sharing and security-role-related access changes should be handled by a System Administrator in the environment. 

---

## 12) Quick Q&A for System Admins

### Q: Can Environment Makers publish bots?

Yes. Makers can build, test, publish and connect agents to supported channels, subject to environment and policy configuration.

### Q: Can Environment Makers share a bot to a security group?

They can share agents with security groups for chat/use where allowed by policy, but broader sharing can still fail if the operation requires security-role assignment or other privileged actions.

### Q: Can a security group be made Editor?

No. Microsoft states **Editor permissions can only be given to individual users**, not security groups.

### Q: What should security groups be used for?

Primarily for **viewer/use/chat** access at scale.

### Q: Can individual permissions be changed?

Yes—individual users are the right target when you need to control collaborative/editor access.

### Q: How do admins validate that a published bot works?

Use **Channels** in Copilot Studio and open/share the install link (especially for Teams/M365) for direct testing.

### Q: Can agents be deployed to SharePoint?

Yes. Microsoft has a dedicated SharePoint deployment flow under **Channels**.

### Q: Are other channels available?

Yes. Microsoft documents additional channels/clients such as Power Pages and custom clients via Direct Line API, but production rollout should still be validated channel by channel. 

---

## 13) Official references

- Microsoft Learn – **Role-based security roles for Dataverse**: https://learn.microsoft.com/en-us/power-platform/admin/database-security 

- Microsoft Learn – **Assign a security role to a user**: https://learn.microsoft.com/en-us/power-platform/admin/assign-security-roles 

- Microsoft Learn – **Configure user security in an environment**: https://learn.microsoft.com/en-us/power-platform/admin/database-security-configure

- Microsoft Learn – **Control how agents are shared**: https://learn.microsoft.com/en-us/microsoft-copilot-studio/admin-sharing-controls-limits

- Microsoft Learn – **Share agents with other users**: https://learn.microsoft.com/en-us/microsoft-copilot-studio/admin-share-bots

- Microsoft Learn – **Connect and configure an agent for Teams and Microsoft 365**: https://learn.microsoft.com/en-us/microsoft-copilot-studio/publication-add-bot-to-microsoft-teams 

- Microsoft Learn – **Publish an agent to SharePoint**: https://learn.microsoft.com/en-us/microsoft-copilot-studio/publication-add-bot-to-sharepoint

- Microsoft Learn – **Publish agents to channels and clients**: https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/channels

- Microsoft Learn – **Key concepts: Publish and deploy your agent**: https://learn.microsoft.com/en-us/microsoft-copilot-studio/publication-fundamentals-publish-channels
