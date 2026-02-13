# Filmmaker Hub

Self-service landing page for GATHR filmmaker relations. Reduces repetitive email inquiries by providing instant answers and structured intake forms.

## Live Site

Once deployed: `https://[yourusername].github.io/gathr-filmmaker-hub`

## Structure

```
├── index.html              # Main landing page
├── data/
│   └── weekly-report.json  # Updated weekly by n8n workflow
└── README.md
```

## How It Works

1. **Visitors** land on the page and self-select their path:
   - Filmmaker wanting to distribute
   - Host wanting to screen a film
   - User needing help/support

2. **FAQs** answer the most common questions (based on email log analysis)

3. **Intake forms** capture structured information before it becomes an email

4. **Weekly report** (updated by n8n) tracks new question patterns

## n8n Integration

### Weekly Report Workflow

Create an n8n workflow that runs weekly:

1. **Trigger**: Cron - Every Monday at 9am
2. **Google Sheets**: Pull emails from past 7 days
3. **Code Node**: Aggregate by category, identify new patterns
4. **GitHub Node**: Update `data/weekly-report.json`

### GitHub Node Setup

1. Create a GitHub Personal Access Token (Settings → Developer Settings → Personal Access Tokens)
2. In n8n, add GitHub credentials with the token
3. Use "Edit File" operation to update the weekly-report.json

Example n8n Code Node for aggregation:

```javascript
const emails = $input.all();

const byCategory = {};
emails.forEach(email => {
  const cat = email.json.category || 'UNKNOWN';
  byCategory[cat] = (byCategory[cat] || 0) + 1;
});

return [{
  json: {
    lastUpdated: new Date().toISOString(),
    weekStart: // calculate,
    weekEnd: // calculate,
    totalEmails: emails.length,
    byCategory: byCategory,
    newQuestions: [], // analyze for new patterns
    topPatterns: []   // most common issues
  }
}];
```

## Customization

### Adding/Removing FAQs

Edit the `anchoredFAQs` object in `index.html`. Set `featured: true` to show, `featured: false` to hide.

### Form Integration

Replace the Formspree placeholders with HubSpot form embeds when ready:

```html
<!-- Replace this -->
<form id="filmmaker-form" action="https://formspree.io/f/YOUR_FORM_ID" method="POST">

<!-- With HubSpot embed -->
<div id="filmmaker-form">
  <script charset="utf-8" type="text/javascript" src="//js.hsforms.net/forms/v2.js"></script>
  <script>
    hbspt.forms.create({
      portalId: "YOUR_PORTAL_ID",
      formId: "YOUR_FORM_ID"
    });
  </script>
</div>
```

## Deployment

### GitHub Pages (Current)

1. Push to `main` branch
2. Go to Settings → Pages
3. Source: Deploy from branch `main`, folder `/ (root)`
4. Site live at `https://[username].github.io/gathr-filmmaker-hub`

### Future: GATHR Domain

When ready to move to official GATHR hosting:
1. Export files
2. Upload to HubSpot CMS or GATHR web server
3. Update DNS/subdomain (e.g., `start.gathr.com`)

## Maintenance

- **Weekly**: Review `weekly-report.json` for new question patterns
- **Monthly**: Update FAQs based on email trends
- **As needed**: Adjust form fields based on intake quality

---

Built by Neil Williams | GATHR Filmmaker Relations
