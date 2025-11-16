---
title: "Deploy Your URL Shortener to Fly.io in Minutes"
description: "A complete guide to deploying the URL Shortener API on Fly.io with persistent storage, custom domains, and monitoring - all within the free tier."
pubDate: 2025-01-25
author: "URL Shortener Team"
tags: ["deployment", "fly.io", "hosting", "devops", "docker"]
draft: false
---

Running your own URL shortener is great, but you need somewhere to host it. Fly.io is an excellent choice - it's fast, affordable, and their free tier is generous enough to run a production URL shortener at no cost.

In this guide, we'll walk through deploying the URL Shortener API to Fly.io with persistent storage, custom domains, and monitoring capabilities.

## Why Fly.io?

Fly.io stands out for several reasons:

### Global Edge Network
Your app runs close to your users with data centers worldwide. This means faster redirects and better user experience.

### Generous Free Tier
The free tier includes 3 VMs with 256MB RAM each, 3GB of persistent storage, and 160GB of outbound data transfer. More than enough for most URL shortener deployments.

### Simple Deployment
Deploy with a single command. No complex configuration, no YAML nightmares, just straightforward deployment.

### Built for Docker
The URL Shortener API comes as a ready-to-use Docker image. Fly.io's Docker-first approach makes deployment seamless.

## Prerequisites

Before we begin, you'll need:

- A Fly.io account (free signup at [fly.io](https://fly.io))
- Flyctl CLI installed on your machine
- A valid URL Shortener API license key

## Step 1: Install and Authenticate Flyctl

First, install the Fly.io CLI:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="install-flyctl" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="install-flyctl"><span class="terminal-prompt">$</span> # macOS/Linux
curl -L https://fly.io/install.sh | sh

<span class="terminal-prompt">$</span> # Windows (PowerShell)
pwsh -Command "iwr https://fly.io/install.ps1 -useb | iex"

<span class="terminal-prompt">$</span> # Authenticate
fly auth login</code></pre>
</div>
</div>

The authentication command will open your browser to complete the login process.

## Step 2: Create Your App

Choose a unique name for your app or let Fly.io generate one:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="create-app" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="create-app"><span class="terminal-prompt">$</span> fly apps create your-app-name

<span class="terminal-prompt">$</span> # Or let Fly.io generate a name
fly apps create</code></pre>
</div>
</div>

Your app will be accessible at `https://your-app-name.fly.dev`.

## Step 3: Configure Your Deployment

Download the `fly.toml` configuration file from the [url-shortener-api repository](https://github.com/dmancevo/url-shortener-api/blob/main/deploy/fly.toml). This file defines your app's configuration.

Key settings to customize:

```toml
app = "your-app-name"
primary_region = "iad"
```

**Available regions:**
- `iad` - Virginia, USA (East Coast)
- `lax` - California, USA (West Coast)
- `fra` - Frankfurt, Germany
- `syd` - Sydney, Australia
- `sin` - Singapore

Choose the region closest to your users for the best performance.

## Step 4: Set Your License Key

Store your license key securely as a secret:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="set-license" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="set-license"><span class="terminal-prompt">$</span> fly secrets set LICENSE_KEY=your-actual-license-key</code></pre>
</div>
</div>

Secrets are encrypted and never exposed in logs or configuration files.

## Step 5: Create Persistent Storage

Your URL data needs to persist across deployments. Create a volume:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="create-volume" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="create-volume"><span class="terminal-prompt">$</span> fly volumes create url_shortener_data \
  --region iad \
  --size 1</code></pre>
</div>
</div>

Important notes about volumes:
- The volume name must match the `source` in `fly.toml` (default: `url_shortener_data`)
- The region must match your app's `primary_region`
- Size is in GB (1 GB minimum, sufficient for thousands of URLs)
- You can increase size later but cannot decrease it

## Step 6: Deploy!

Now for the moment of truth:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="deploy" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="deploy"><span class="terminal-prompt">$</span> fly deploy</code></pre>
</div>
</div>

Fly.io will pull the Docker image, configure your app, mount the persistent volume at `/data`, and start your service. The whole process takes about 30 seconds.

## Step 7: Get Your API Key

Your URL shortener generates a random API key on first startup. Retrieve it from the logs:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="get-logs" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="get-logs"><span class="terminal-prompt">$</span> fly logs</code></pre>
</div>
</div>

Look for output showing the generated API key. **Save this securely** - you'll need it for all API requests.

## Step 8: Test Your Deployment

Verify everything is working:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="test-deployment" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="test-deployment"><span class="terminal-prompt">$</span> # Check status
fly status
<span class="terminal-prompt">$</span> # Create a short URL
curl -X POST https://your-app-name.fly.dev/shorten \
  -H "X-API-Key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com"}'
<span class="terminal-prompt">$</span> # Test the redirect
curl -L https://your-app-name.fly.dev/abc12345</code></pre>
</div>
</div>

If you see a successful redirect, congratulations! Your URL shortener is live.

## Advanced Configuration

### Enable Click Tracking

Track analytics for your shortened URLs:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="click-tracking" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="click-tracking"><span class="terminal-prompt">$</span> fly secrets set CLICK_TRACKING_PATH=/data/clicks.jsonl</code></pre>
</div>
</div>

**Note:** Review GDPR and privacy considerations before enabling tracking in production.

### Add a Custom Domain

Brand your short URLs with your own domain:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="custom-domain" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="custom-domain"><span class="terminal-prompt">$</span> fly certs add yourdomain.com</code></pre>
</div>
</div>

Follow the instructions to add the required DNS records (A and AAAA) to your domain registrar. Fly.io will automatically provision SSL certificates.

### Scale Your Resources

Need more power? Scale vertically or horizontally:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="scaling" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="scaling"><span class="terminal-prompt">$</span> # Increase memory
fly scale memory 512

<span class="terminal-prompt">$</span> # Add more instances
fly scale count 2

<span class="terminal-prompt">$</span> # Upgrade to dedicated CPU
fly scale vm dedicated-cpu-1x</code></pre>
</div>
</div>

The default configuration (256MB, shared CPU) handles thousands of redirects per day comfortably.

## Backup and Monitoring

### Create Backups

Back up your URL data regularly:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="backup" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="backup"><span class="terminal-prompt">$</span> fly ssh console -C "tar -czf /tmp/backup.tar.gz /data"
fly ssh sftp get /tmp/backup.tar.gz ./backup.tar.gz</code></pre>
</div>
</div>

Consider setting up a cron job to automate this process.

### Monitor Logs

Keep an eye on your app with real-time logs:

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="monitor-logs" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="monitor-logs"><span class="terminal-prompt">$</span> # Follow live logs
fly logs -f

<span class="terminal-prompt">$</span> # View recent logs
fly logs --recent</code></pre>
</div>
</div>

## Troubleshooting

### App Not Starting?

Check these common issues:

1. **Verify your license key:** `fly secrets list`
2. **Check the logs:** `fly logs`
3. **Confirm volume is mounted:** `fly volumes list`

### Volume Not Mounting?

Ensure the volume name in your `fly.toml` matches the volume you created, and that both are in the same region.

### Need More Storage?

Expand your volume (you can increase but not decrease):

<div class="quick-start">
<div class="terminal-header">
<div class="terminal-buttons">
<span class="terminal-button terminal-close"></span>
<span class="terminal-button terminal-minimize"></span>
<span class="terminal-button terminal-maximize"></span>
</div>
<div class="terminal-title">Terminal</div>
<button class="copy-button" data-copy-target="expand-volume" aria-label="Copy code">
<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M5.5 4.5V2.5C5.5 1.94772 5.94772 1.5 6.5 1.5H13.5C14.0523 1.5 14.5 1.94772 14.5 2.5V9.5C14.5 10.0523 14.0523 10.5 13.5 10.5H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
<rect x="1.5" y="5.5" width="8" height="9" rx="1" stroke="currentColor" stroke-width="1.5"/>
</svg>
<span class="copy-text">Copy</span>
</button>
</div>
<div class="terminal-body">
<pre><code id="expand-volume"><span class="terminal-prompt">$</span> fly volumes list
fly volumes extend &lt;volume-id&gt; --size 5</code></pre>
</div>
</div>

## Cost Optimization

The default configuration fits comfortably within Fly.io's free tier:

- **3 shared-cpu-1x VMs** (256MB RAM each)
- **3GB persistent storage**
- **160GB outbound data transfer**

To minimize costs:

1. Use the `auto_stop_machines = true` setting (enabled by default in the provided `fly.toml`)
2. Start with minimal resources and scale as needed
3. Use shared CPUs instead of dedicated ones
4. Monitor your usage via `fly dashboard`

For most use cases, the free tier is more than sufficient for production workloads.

## Conclusion

Deploying your URL shortener to Fly.io gives you a globally distributed, fast, and reliable service with minimal effort. The combination of Docker-based deployment, persistent storage, and automatic SSL makes Fly.io an excellent choice for hosting your URL shortener.

The free tier is generous enough for serious production use, and you can scale up seamlessly as your needs grow. Plus, with custom domains and click tracking, you have all the features of commercial URL shorteners while maintaining complete control over your data.

Ready to deploy? Follow this guide and you'll have your URL shortener running in production in less than 10 minutes!

---

*For more information, check out the [URL Shortener API documentation](https://github.com/dmancevo/url-shortener-api) and [Fly.io documentation](https://fly.io/docs/).*
