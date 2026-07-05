> From: https://gemini.google.com/app/5f8d5840f35318e2?utm_source=app_launcher&utm_medium=owned&utm_campaign=base_all&hl=en_GB

# you asked

message time: 2026-07-05 10:26:10

developer a google apps script to send different email content on different weekdays. For exmaple, if weekday number is 1, then email_body should "Today is Mon". Below is a dict of all required content. also, email subject should be based on day number as well, but email recipient is always the same. action now.

"""
email_body = {
1 :  "Today is Mon",
2 :  "Today is Tue",
3 : ""Today is Wed"
}
"""

---

# gemini response

Let's get this automated right now. 

Here is the Google Apps Script to handle this. It uses JavaScript's built-in `getDay()` method, which conveniently maps Monday to 1, Tuesday to 2, and so on (with Sunday being 0).

### **Google Apps Script**

```javascript
function sendDailyEmail() {
  // Set your static recipient here
  const recipient = "recipient@example.com"; 
  
  // Get the current day of the week (0 = Sunday, 1 = Monday, ..., 6 = Saturday)
  const dayNumber = new Date().getDay();

  // Dictionary for email bodies
  const emailBodies = {
    0: "Today is Sun",
    1: "Today is Mon",
    2: "Today is Tue",
    3: "Today is Wed",
    4: "Today is Thu",
    5: "Today is Fri",
    6: "Today is Sat"
  };

  // Dictionary for email subjects
  const emailSubjects = {
    0: "Subject for Sunday",
    1: "Subject for Monday",
    2: "Subject for Tuesday",
    3: "Subject for Wednesday",
    4: "Subject for Thursday",
    5: "Subject for Friday",
    6: "Subject for Saturday"
  };

  // Retrieve the specific subject and body for today
  const body = emailBodies[dayNumber];
  const subject = emailSubjects[dayNumber];

  // Send the email if the day exists in our dictionaries
  if (body && subject) {
    GmailApp.sendEmail(recipient, subject, body);
  }
}
```

### **How to Deploy and Automate**

1. Go to [script.google.com](https://script.google.com/) and create a **New Project**.
2. Delete any code in the editor and paste the script above. 
3. Update the `recipient` variable, as well as the text inside `emailBodies` and `emailSubjects` to match your exact needs.
4. Click the **Save** icon (floppy disk).
5. **Set the Trigger** so it runs automatically:
   * Click the **clock icon (Triggers)** on the left sidebar.
   * Click **+ Add Trigger** in the bottom right.
   * Choose which function to run: `sendDailyEmail`
   * Select event source: `Time-driven`
   * Select type of time based trigger: `Day timer`
   * Select time of day: Choose your preferred time slot (e.g., `8am to 9am`).
   * Click **Save** (you will be prompted to grant permissions to your Google Account).

---

# you asked

message time: 2026-07-05 10:29:54

explian `GmailApp.sendEmail` API and all its arguments to me

---

# gemini response

Let's break down the `GmailApp.sendEmail` method. It is the powerhouse behind sending automated emails in Google Apps Script, and it's highly customizable depending on what you need to send. 

The method has a few different versions (called overloads), but they all build upon the same core structure.

### **The Basic Structure**

At its simplest, the API requires three mandatory arguments to send an email. 

```javascript
GmailApp.sendEmail(recipient, subject, body)
```

* **`recipient`** (String): The email address of the person receiving the email. You can send it to multiple people by separating the email addresses with a comma (e.g., `"person1@example.com, person2@example.com"`).
* **`subject`** (String): The subject line of the email. 
* **`body`** (String): The plain text content of the email. Even if you plan to send a fancy HTML email, you *must* provide this plain text string as a fallback for email clients that can't render HTML.

---

### **The Advanced Structure (The `options` Object)**

If you want to add CCs, BCCs, attachments, or use HTML formatting, you add a fourth argument called `options`. 

```javascript
GmailApp.sendEmail(recipient, subject, body, options)
```

* **`options`** (Object): A JavaScript object containing key-value pairs for advanced settings. 

Here are all the arguments you can place inside the `options` object:

| Argument | Type | What it does |
| :--- | :--- | :--- |
| **`attachments`** | Array of Blobs | Files you want to attach (like PDFs, Google Sheets converted to PDFs, or images). |
| **`bcc`** | String | A comma-separated list of email addresses to blind carbon copy. |
| **`cc`** | String | A comma-separated list of email addresses to carbon copy. |
| **`from`** | String | The address the email should be sent from. **Note:** You can't just put any email here; it must be an alias currently verified and linked to your Gmail account. |
| **`htmlBody`** | String | The HTML version of the email body. If this is provided, it overrides the plain text `body` argument (though the plain text is still kept as a fallback). |
| **`inlineImages`** | Object | A dictionary mapping image keys (CIDs) to image blobs. Used to embed images directly into the HTML body rather than attaching them. |
| **`name`** | String | The display name of the sender (e.g., "Automated Bot" instead of your raw email address). |
| **`replyTo`** | String | The email address where replies should be sent, if different from the sender. |
| **`noReply`** | Boolean | Set to `true` to send from a generic no-reply address. **Note:** This only works for Google Workspace (formerly G Suite) enterprise/education accounts, not standard free Gmail accounts. |

---

### **Putting It All Together: Code Examples**

**1. A Simple Plain-Text Email**
```javascript
function sendSimpleEmail() {
  GmailApp.sendEmail(
    "friend@example.com", 
    "Lunch today?", 
    "Hey! Are we still on for lunch at noon?"
  );
}
```

**2. An Advanced Email with HTML, CC, and a Custom Name**
```javascript
function sendAdvancedEmail() {
  const recipient = "boss@example.com";
  const subject = "Weekly Report";
  
  // The mandatory plain text fallback
  const plainTextBody = "Please view this email in an HTML-compatible client."; 
  
  // The fancy HTML version
  const htmlContent = "<h2>Weekly Report</h2><p>Here are the <strong>final</strong> numbers for the week.</p>";

  // The options object
  const advancedOptions = {
    htmlBody: htmlContent,
    cc: "coworker@example.com",
    name: "Automated Reporting System",
    replyTo: "myrealemail@example.com"
  };

  // Sending the email
  GmailApp.sendEmail(recipient, subject, plainTextBody, advancedOptions);
}
```

**One important reality check:** Google Apps Script has daily quotas. For a standard free Gmail account, you can send emails to a maximum of 100 recipients per day. If you are on a paid Google Workspace account, that limit is usually 1,500 per day. If you hit that limit, the script will throw an error and stop working until the next day.