# Reflector Worker

A lightweight HTTP request reflector/echo service built with Cloudflare Workers and Hono framework. This worker mirrors incoming HTTP requests, making it perfect for debugging webhooks, testing HTTP clients, and inspecting request details.

## 🎯 Features

- **Request Mirroring**: Reflects all HTTP headers and body content back to the client
- **Universal Route Handling**: Catches all routes and HTTP methods
- **Header Preservation**: Returns the exact headers received from the request
- **Body Echo**: For POST/PUT/PATCH requests, returns the request body unchanged
- **Error Handling**: Comprehensive error handling with detailed error responses
- **Lightweight**: Built on Cloudflare Workers for global edge deployment

## 🚀 Quick Start

### Prerequisites

- Node.js (v18 or higher)
- pnpm package manager (v10.15.1)
- Cloudflare account (for deployment)

### Installation

1. Clone the repository:

```bash
git clone <repository-url>
cd reflector-worker
```

2. Install dependencies:

```bash
pnpm install
```

3. Start development server:

```bash
pnpm dev
```

The worker will be available at `http://localhost:8787`

## 📖 How It Works

The reflector worker processes incoming requests as follows:

- **GET/HEAD Requests**: Returns a 200 status with the original request headers
- **Other Methods** (POST, PUT, PATCH, DELETE, etc.): Returns the request body along with the original headers
- **All Requests**: Logs headers to console for debugging purposes

## 🛠️ Available Scripts

| Script            | Description                                       |
| ----------------- | ------------------------------------------------- |
| `pnpm dev`        | Start local development server                    |
| `pnpm start`      | Alias for `pnpm dev`                              |
| `pnpm deploy`     | Deploy to Cloudflare Workers                      |
| `pnpm cf-typegen` | Generate TypeScript types for Cloudflare bindings |

## 💻 Usage Examples

### Test with cURL

**GET Request:**

```bash
curl -i http://localhost:8787/test \
  -H "X-Custom-Header: Hello"
```

**POST Request with JSON:**

```bash
curl -X POST http://localhost:8787/echo \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello, World!"}'
```

**Testing Webhook:**

```bash
curl -X POST http://localhost:8787/webhook \
  -H "X-Webhook-Secret: my-secret" \
  -H "Content-Type: application/json" \
  -d '{"event": "user.created", "data": {"id": 123}}'
```

### Response Format

For successful requests, the worker returns:

- Status: 200
- Headers: Exact copy of request headers
- Body: Request body (for non-GET/HEAD methods)

For errors:

```json
{
	"error": {
		"message": "Error description",
		"type": "Error type",
		"stack": "Stack trace (in development)"
	},
	"metadata": {
		"timestamp": "2025-09-02T12:00:00Z",
		"path": "/requested/path",
		"method": "POST"
	}
}
```

## 🚢 Deployment

### Deploy to Cloudflare Workers

1. Configure your Cloudflare account:

```bash
wrangler login
```

2. Deploy the worker:

```bash
pnpm deploy
```

3. Your worker will be available at:

```
https://reflector-worker.<your-subdomain>.workers.dev
```

### Custom Domain (Optional)

To use a custom domain, uncomment and configure the route in `wrangler.jsonc`:

```jsonc
{
	"route": {
		"pattern": "reflector.yourdomain.com/*",
		"custom_domain": true
	}
}
```

## 🏗️ Project Structure

```
reflector-worker/
├── src/
│   └── index.ts        # Main worker implementation
├── package.json        # Dependencies and scripts
├── wrangler.jsonc      # Cloudflare Workers configuration
├── tsconfig.json       # TypeScript configuration
└── README.md          # This file
```

## 🔧 Configuration

The worker configuration is managed through `wrangler.jsonc`:

- **name**: Worker name (reflector-worker)
- **main**: Entry point (src/index.ts)
- **compatibility_date**: Cloudflare Workers compatibility date
- **workers_dev**: Enable workers.dev subdomain
- **observability**: Enable Cloudflare observability features

## 📝 Use Cases

- **Webhook Development**: Test webhook endpoints by inspecting exact payloads
- **HTTP Client Testing**: Verify headers and body content sent by HTTP clients
- **API Debugging**: Debug API requests by examining exact request details
- **Request Inspection**: Analyze request headers, authentication tokens, and payloads
- **Integration Testing**: Mock external services during development

## 🛡️ Security Considerations

- This worker echoes all request data, including potentially sensitive headers
- Not recommended for production use with sensitive data
- Consider adding authentication if deploying publicly
- Be aware of potential data exposure when using for debugging

## 📦 Dependencies

- **[Hono](https://hono.dev/)** (v4.9.5): Lightweight web framework
- **[Wrangler](https://developers.cloudflare.com/workers/wrangler/)** (v4.33.1): Cloudflare Workers CLI
