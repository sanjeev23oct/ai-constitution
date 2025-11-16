# GitHub Pages Setup Instructions

## Enable GitHub Pages

Follow these steps to make your prototypes accessible via live URLs:

### Step 1: Go to Repository Settings

1. Open your repository: https://github.com/sanjeev23oct/ai-constitution
2. Click on **Settings** tab (top right)

### Step 2: Enable GitHub Pages

1. In the left sidebar, click **Pages** (under "Code and automation")
2. Under **Source**, select:
   - **Branch**: `main`
   - **Folder**: `/ (root)`
3. Click **Save**

### Step 3: Wait for Deployment

- GitHub will build and deploy your site (takes 1-2 minutes)
- You'll see a message: "Your site is live at https://sanjeev23oct.github.io/ai-constitution/"

### Step 4: Share URLs with Business Users

Once deployed, share these live URLs:

#### Main Landing Page
```
https://sanjeev23oct.github.io/ai-constitution/
```

#### CRM Dashboard Prototype
```
https://sanjeev23oct.github.io/ai-constitution/examples/crm-dashboard-prototype.html
```

#### RM Account Hierarchy Prototype
```
https://sanjeev23oct.github.io/ai-constitution/examples/rm-account-hierarchy-prototype.html
```

## Benefits of GitHub Pages

✅ **Free Hosting** - No cost for public repositories
✅ **HTTPS Enabled** - Secure by default
✅ **Fast CDN** - Global content delivery
✅ **Easy Updates** - Just push to main branch
✅ **Professional URLs** - Share with stakeholders
✅ **No Server Management** - GitHub handles everything

## Updating Prototypes

To update a prototype:

1. Edit the HTML file locally
2. Commit and push to main branch
3. GitHub Pages automatically rebuilds (1-2 minutes)
4. Changes are live!

## Custom Domain (Optional)

If you want a custom domain like `prototypes.yourcompany.com`:

1. Go to Settings → Pages
2. Under "Custom domain", enter your domain
3. Add DNS records as instructed
4. Enable "Enforce HTTPS"

## Troubleshooting

### Site Not Loading?

- Wait 2-3 minutes after enabling Pages
- Check Settings → Pages for deployment status
- Ensure `index.html` is in the root directory

### 404 Error?

- Verify file paths are correct
- Check that files are committed and pushed
- Ensure branch is set to `main` in Pages settings

### Changes Not Showing?

- Wait 1-2 minutes for rebuild
- Hard refresh browser (Ctrl+Shift+R or Cmd+Shift+R)
- Check GitHub Actions tab for build status

## Sharing with Business Users

### Email Template

```
Subject: Interactive Prototypes for Review

Hi [Name],

I've created interactive prototypes for your review:

🏠 Main Page: https://sanjeev23oct.github.io/ai-constitution/

📊 CRM Dashboard: 
https://sanjeev23oct.github.io/ai-constitution/examples/crm-dashboard-prototype.html

🏦 RM Account Hierarchy: 
https://sanjeev23oct.github.io/ai-constitution/examples/rm-account-hierarchy-prototype.html

These are fully functional prototypes - you can click, interact, and test all features.

Please review and provide feedback by [date].

Best regards,
[Your Name]
```

### QR Code (Optional)

Generate QR codes for easy mobile access:
- Use https://qr-code-generator.com/
- Enter your prototype URL
- Print or share QR code

## Security Note

⚠️ **Important**: These prototypes are publicly accessible. Do not include:
- Real customer data
- Actual account numbers
- Sensitive business information
- Production credentials

Use mock data only (as currently implemented).

## Next Steps

1. Enable GitHub Pages (Steps above)
2. Wait for deployment
3. Test all prototype URLs
4. Share with business stakeholders
5. Gather feedback
6. Iterate and update as needed

---

**Questions?** Open an issue on GitHub or contact the repository owner.
