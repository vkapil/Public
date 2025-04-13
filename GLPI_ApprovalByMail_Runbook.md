# Runbook: Configuring ApprovalByMail Plugin in GLPI

## Objective
Configure the ApprovalByMail plugin in GLPI to enable email-based approval workflows, where approvers can approve or reject tickets directly from the email using secure links.

---

## Prerequisites

- GLPI installed and operational
- FormCreator plugin installed and configured
- ApprovalByMail plugin installed and enabled
- Working email notification setup (SMTP configured in GLPI)
- Publicly accessible GLPI base URL (important for link access in emails)

---

## 1. Plugin Installation and Activation

### Steps:

1. Copy the plugin to the GLPI plugins directory:

```bash
cp -r approvalbymail /var/www/html/glpi/plugins/
```

2. Set appropriate permissions:

```bash
chown -R www-data:www-data /var/www/html/glpi/plugins/approvalbymail
chmod -R 755 /var/www/html/glpi/plugins/approvalbymail
```

3. Log into GLPI as an administrator.
4. Navigate to: **Setup > Plugins**
5. Locate the ApprovalByMail plugin and click **Install**, then click **Enable**.

---

## 2. Plugin Configuration

### Steps:

1. Go to: **Administration > Plugins > Approval by Mail > Settings**
2. Configure the following options:

- **Enable email approval**: Check this box.
- **Base URL**: Provide your public GLPI URL (e.g., `https://glpi.example.com`).
- **Token lifetime**: Optional. Defines how long the approval token is valid (e.g., `24h`).
- **Allowed IPs**: Optional. List allowed IP addresses for security (optional).

---

## 3. Modify Notification Template

1. Navigate to: **Setup > Notifications > Notification templates**
2. Locate and edit the template: **Validation request to approve**
3. Add or modify the content like this:

```text
Hello,

You have a pending approval request for the following ticket: ##ticket.title##

Please choose an action:
Approve: ##ticket.validation.accept.url##
Reject: ##ticket.validation.reject.url##

Ticket Description:
##ticket.content##
```

4. Save the changes.


---

## 4. Configure Notification Event

1. Go to: **Setup > Notifications > Notifications**
2. Ensure the event **Validation request to approve** is active.
3. Assign the correct notification template (from the previous step).
4. Add appropriate recipients (users, groups, roles).

---

## 5. Configure FormCreator for Sequential Approval

1. Navigate to: **Administration > Forms**
2. Edit the form that will be used for change requests.
3. In the **Target Ticket** section, configure the following:

- Set the **validation process**.
- Add multiple validation levels (e.g., Team Lead, Manager, Department Head).
- Ensure **sequential flow** is selected (each approver is notified only after the previous one approves).

4. Save the form configuration.

---

## 6. Test the Full Workflow

1. Submit the change request form using FormCreator.
2. A GLPI ticket is generated.
3. The first validator receives an email with approval/rejection links.
4. The validator clicks the link to **approve** or **reject**.
5. Status updates in GLPI automatically.
6. If approved, the next validator receives the same style email.
7. After final approval, the ticket is routed to the support group or assigned technician as configured.

---

## 7. Validation Logging and Traceability

- Each validation step is logged in the **Validation** tab of the ticket.
- All actions via email are traceable with timestamps.
- You can enable logging in GLPI for additional auditing if needed.

---

## Notes

- Make sure your GLPI instance is accessible via the internet or the internal network based on the user base.
- Only registered users in GLPI can perform approvals via email.
- Token expiration and IP filtering can be used to enhance security.
- If notifications are not delivered, verify SMTP setup and user email correctness.

---

## Reference Links

- ApprovalByMail Plugin Documentation:  
  https://glpi-plugins.readthedocs.io/en/latest/approvalbymail/index.html

- Official Announcement:  
  https://glpi-project.org/approvalbymail-new-plugin-for-glpi/

- ApprovalByMail FAQ:  
  https://faq.teclib.com/04_Plugins/Approval_mail/
