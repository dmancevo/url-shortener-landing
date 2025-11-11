# URL Shortener with A/B Testing

A high-performance URL shortener service with built-in A/B testing capabilities, built in Rust for speed and reliability. Features include weighted traffic distribution, URL expiration, optional click tracking, and tag-based organization.

## Quick Start with Docker

### Running the Server

The easiest way to run the URL shortener is using Docker:

```bash
docker run -d \
  -e LICENSE_KEY=your-license-key \
  -p 8080:3000 \
  -v $(pwd)/data:/data \
  ghcr.io/dmancevo/url-shortener:latest
```

**Important:** The server requires a valid license key to run. You can get a license at [www.url-shortener-api.com](https://www.url-shortener-api.com). Set your license key via the `LICENSE_KEY` environment variable.

### Getting Your API Key

When the server starts, it generates a unique API key and displays it prominently in the logs:

```bash
# View the logs to get your API key
docker logs <container-id>
```

You'll see output like:

```
================================================================================
================================================================================
                         API KEY GENERATED
================================================================================

    AbCdEfGh1234567890XyZaBcDeFgHi

    Include this key in the 'X-API-Key' header for all API requests.
    Redirects (GET /:code) do not require authentication.
================================================================================
================================================================================
```

Copy this API key - you'll need it for all API requests (except redirects).

## Configuration

Customize the server using environment variables:

```bash
docker run -d \
  -e LICENSE_KEY=your-license-key \
  -e HOST=0.0.0.0 \
  -e PORT=3000 \
  -e CACHE_CAPACITY=1000 \
  -e PARTITION_COUNT=10 \
  -e STORAGE_PATH=/data/urls.json \
  -e CLICK_TRACKING_PATH=/data/clicks.jsonl \
  -p 8080:3000 \
  -v $(pwd)/data:/data \
  ghcr.io/dmancevo/url-shortener:latest
```

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `LICENSE_KEY` | License key (**required**) - Get yours at [www.url-shortener-api.com](https://www.url-shortener-api.com) | None |
| `HOST` | Server host | `0.0.0.0` |
| `PORT` | Server port | `3000` |
| `CACHE_CAPACITY` | Max entries in LRU cache | `1000` |
| `PARTITION_COUNT` | Number of storage partitions for improved performance | `10` |
| `STORAGE_PATH` | Path to JSON storage file | `./data/urls.json` |
| `CLICK_TRACKING_PATH` | Path to click tracking file (optional, enables click tracking) | None |

## API Endpoints

All endpoints except `GET /:code` require the `X-API-Key` header for authentication.

| Method | Endpoint | Auth Required | Description |
|--------|----------|---------------|-------------|
| POST | `/shorten` | Yes | Create a short URL |
| GET | `/:code` | No | Redirect to target URL |
| PUT | `/:code` | Yes | Update a short URL |
| DELETE | `/:code` | Yes | Delete a short URL |
| POST | `/api/codes-by-url` | Yes | Find short codes by URL |
| POST | `/api/codes-by-tag` | Yes | Find short codes by tag |
| GET | `/api/stats` | Yes | Get cache statistics |

## Usage Examples

Replace `YOUR_API_KEY_HERE` with the API key displayed when you started the server.

### Basic URL Shortening

Create a short URL:

```bash
curl -X POST http://localhost:8080/shorten \
  -H "X-API-Key: YOUR_API_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com/very/long/url"}'
```

Response:
```json
{
  "short_code": "abc12345",
  "url": "https://example.com/very/long/url"
}
```

Access the short URL (no authentication needed):
```bash
# Follow the redirect
curl -L http://localhost:8080/abc12345

# View redirect header without following
curl -I http://localhost:8080/abc12345
```

### URL Shortening with Tags

Add tags to organize your short URLs:

```bash
curl -X POST http://localhost:8080/shorten \
  -H "X-API-Key: YOUR_API_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://example.com/landing-page",
    "tag": "marketing-campaign"
  }'
```

Response:
```json
{
  "short_code": "def67890",
  "url": "https://example.com/landing-page",
  "tag": "marketing-campaign"
}
```

Find all short codes with a specific tag:

```bash
curl -X POST http://localhost:8080/api/codes-by-tag \
  -H "X-API-Key: YOUR_API_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{"tag": "marketing-campaign"}'
```

Response:
```json
{
  "tag": "marketing-campaign",
  "short_codes": ["def67890", "ghi11121"]
}
```

### URL Expiration

Create temporary short URLs that automatically expire:

```bash
# Expires in 2 hours
curl -X POST http://localhost:8080/shorten \
  -H "X-API-Key: YOUR_API_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://example.com/temporary-sale",
    "expires_in": "2h"
  }'
```

Expiration format:
- `"5m"` - 5 minutes
- `"2h"` - 2 hours
- `"3d"` - 3 days

### A/B Testing

Split traffic between multiple URLs with weighted distribution:

```bash
# 50/50 split
curl -X POST http://localhost:8080/shorten \
  -H "X-API-Key: YOUR_API_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "urls": [
      {"url": "https://example.com/variant-a", "weight": 0.5},
      {"url": "https://example.com/variant-b", "weight": 0.5}
    ]
  }'
```

```bash
# 70/30 split with tag and expiration
curl -X POST http://localhost:8080/shorten \
  -H "X-API-Key: YOUR_API_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "urls": [
      {"url": "https://example.com/variant-a", "weight": 0.7},
      {"url": "https://example.com/variant-b", "weight": 0.3}
    ],
    "tag": "homepage-test",
    "expires_in": "7d"
  }'
```

**Note:** Weights must sum to 1.0, and the same visitor (identified by IP address) will always see the same variant.

### Update Short URL

Modify the target URL, tag, or expiration:

```bash
curl -X PUT http://localhost:8080/abc12345 \
  -H "X-API-Key: YOUR_API_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://example.com/new-destination",
    "tag": "updated-campaign",
    "expires_in": "24h"
  }'
```

### Delete Short URL

```bash
curl -X DELETE http://localhost:8080/abc12345 \
  -H "X-API-Key: YOUR_API_KEY_HERE"
```

### Find Short Codes by URL

Get all short codes pointing to a specific URL:

```bash
curl -X POST http://localhost:8080/api/codes-by-url \
  -H "X-API-Key: YOUR_API_KEY_HERE" \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com/landing-page"}'
```

Response:
```json
{
  "url": "https://example.com/landing-page",
  "short_codes": ["abc123", "xyz789"]
}
```

### Get Statistics

View cache statistics:

```bash
curl http://localhost:8080/api/stats \
  -H "X-API-Key: YOUR_API_KEY_HERE"
```

Response:
```json
{
  "cache_size": 42,
  "cache_capacity": 1000
}
```

## Features

- **A/B Testing**: Split traffic between multiple URLs with configurable weights
- **Deterministic Routing**: Same visitor always sees the same variant (based on IP hash)
- **URL Expiration**: Set automatic expiration times for temporary links
- **Tag Support**: Organize and categorize short URLs with optional tags
- **Click Tracking (Optional)**: Privacy-conscious analytics with IP truncation
- **High Performance**: LRU cache with partitioned storage for millions of URLs
- **API Key Authentication**: Secure API endpoints with automatic key generation

## Click Tracking (Optional)

To enable click tracking, add the `CLICK_TRACKING_PATH` environment variable:

```bash
docker run -d \
  -e LICENSE_KEY=your-license-key \
  -e CLICK_TRACKING_PATH=/data/clicks.jsonl \
  -p 8080:3000 \
  -v $(pwd)/data:/data \
  ghcr.io/dmancevo/url-shortener:latest
```

Click data is stored in JSONL format with privacy protections (IP truncation). Each click record includes:
- Timestamp
- Browser and OS information
- Device category
- Truncated IP address (last 2 octets removed for IPv4)
- Short code accessed

### GDPR Considerations

If you serve users in the EU/EEA and enable click tracking, GDPR may apply. Even with IP truncation, the collected data (truncated IP, browser info, timestamps) may be considered personal data under GDPR. Consider implementing a privacy policy disclosing what data is collected and why, establishing a data retention policy, and ensuring you have a legal basis for processing (such as legitimate interest or consent). Click tracking is most appropriate for internal/corporate use, personal projects, or when users are aware of the analytics. For public-facing services in privacy-conscious regions, carefully evaluate compliance requirements before enabling this feature.

## Using as a Base Image

Create custom deployments by extending the base image:

```dockerfile
FROM ghcr.io/dmancevo/url-shortener:latest

# Set default environment variables
ENV HOST=0.0.0.0
ENV PORT=3000
ENV CACHE_CAPACITY=2000
ENV PARTITION_COUNT=20

# Expose the port
EXPOSE 3000
```

Build and run:

```bash
docker build -t my-url-shortener .
docker run -d \
  -e LICENSE_KEY=your-license-key \
  -p 8080:3000 \
  -v $(pwd)/data:/data \
  my-url-shortener
```

## Performance & Architecture

- **Cache Layer**: LRU cache for fast lookups with configurable capacity
- **Partitioned Storage**: Hash-based partitioning across multiple JSON files for improved write performance
- **Concurrent Operations**: Thread-safe operations using Rust's parking_lot
- **Scalability**: Supports millions of URLs with partitioned storage (default: 10 partitions)
- **Auto-Migration**: Existing single-file storage automatically migrates to partitions on startup

## Error Responses

The API returns standard HTTP status codes:

| Status | Description |
|--------|-------------|
| `200 OK` | Successful operation |
| `301 Moved Permanently` | Redirect (for short URL access) |
| `400 Bad Request` | Invalid URL, weights, or expiration format |
| `401 Unauthorized` | Invalid or missing API key |
| `404 Not Found` | Short code doesn't exist or has expired |
| `500 Internal Server Error` | Storage or server error |

Example error response:
```json
{
  "error": "Invalid or missing API key"
}
```

## License

This software requires a valid license key to run. Get your license at [www.url-shortener-api.com](https://www.url-shortener-api.com).
