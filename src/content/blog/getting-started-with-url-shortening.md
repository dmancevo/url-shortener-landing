---
title: "Getting Started with Self-Hosted URL Shortening"
description: "Learn how to set up your own URL shortener and why self-hosting gives you more control over your links and analytics."
pubDate: 2025-01-15
author: "URL Shortener Team"
tags: ["url-shortening", "self-hosted", "getting-started", "docker"]
draft: false
---

URL shorteners have become an essential tool for modern marketing, but most businesses rely on third-party services like bit.ly or TinyURL. While convenient, these services come with limitations: lack of control, privacy concerns, and potential link rot if the service shuts down.

## Why Self-Host Your URL Shortener?

Self-hosting your URL shortener provides several key advantages:

### Complete Control
With a self-hosted solution, you own your links. No more worrying about a third-party service changing their terms, shutting down, or holding your data hostage.

### Enhanced Privacy
Your link data stays on your servers. You decide who has access to your analytics and how long to retain the data.

### Custom Branding
Use your own domain for shortened URLs. Instead of `bit.ly/abc123`, you can have `yourbrand.com/abc123`, which builds trust and brand recognition.

### Cost-Effective at Scale
Most URL shortening services charge based on the number of links or clicks. With self-hosting, your costs are predictable and don't scale with usage.

## Setting Up Your URL Shortener

Getting started is straightforward with Docker:

```bash
docker run -d \
  -p 8080:8080 \
  -v /path/to/data:/data \
  ghcr.io/dmancevo/url-shortener:latest
```

That's it! Your URL shortener is now running and ready to create links.

## Key Features to Look For

When choosing or building a URL shortener, consider these essential features:

1. **A/B Testing** - Test different destinations to optimize conversions
2. **Analytics** - Track clicks, referrers, devices, and geographic data
3. **Custom Slugs** - Create memorable, branded short URLs
4. **API Access** - Integrate with your existing tools and workflows
5. **Bulk Operations** - Create and manage multiple links efficiently

## Next Steps

Once your URL shortener is running, you can:

- Configure custom domains for branded links
- Set up A/B testing to optimize your marketing campaigns
- Integrate with your analytics platform
- Create links via API for automation

Self-hosting your URL shortener is a powerful way to take control of your links, protect your privacy, and build a stronger brand presence. Give it a try and see the difference it makes!

## Additional Resources

- [URL-Shortener](https://github.com/dmancevo/url-shortener/pkgs/container/url-shortener)
- API Documentation (coming soon)

---

Have questions about setting up your self-hosted URL shortener? Drop us a message on GitHub!
