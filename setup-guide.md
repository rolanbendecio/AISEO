# AI SEO by Diggity - Setup Guide

## Quick Start

1. **Import Workflow**: Import `ai-seo-workflow.json` into your n8n instance
2. **Configure Credentials**: Set up Anthropic API credentials
3. **Set Environment Variables**: Configure your API keys and URLs
4. **Test**: Send a test webhook to verify everything works

## Detailed Setup

### 1. n8n Configuration

#### Import the Workflow
- Open your n8n interface
- Go to Workflows → Import from File
- Select `ai-seo-workflow.json`
- Click Import

#### Set up Anthropic Credentials
- Go to Settings → Credentials
- Add new credential: "Anthropic API"
- Enter your Anthropic API key
- Save and test the connection

### 2. Environment Variables

Create a `.env` file (use `.env.example` as template):

```bash
# Required: Your Anthropic Claude API Key
ANTHROPIC_API_KEY=sk-ant-api03-your-key-here

# Required: Your website URL for log access
WEBSITE_URL=https://your-website.com

# Required: API key for accessing your server logs
API_KEY=your_website_api_key

# Required: Webhook URL to receive results
WEBHOOK_URL=https://your-results-endpoint.com/webhook

# Optional: Claude model (default: claude-3-5-sonnet-20241022)
CLAUDE_MODEL=claude-3-5-sonnet-20241022
```

### 3. Server Log Access

Ensure your website can provide server logs via API:

```bash
GET /server-logs
Authorization: Bearer your_api_key
```

Expected log format: Standard Apache/Nginx access logs with bot user agents.

### 4. Webhook Endpoint

Set up an endpoint to receive workflow results:

```javascript
// Example Node.js endpoint
app.post('/webhook', (req, res) => {
  const {
    log_analysis,
    structured_data,
    content_optimization,
    timestamp
  } = req.body;
  
  // Process the AI SEO recommendations
  console.log('Log Analysis:', log_analysis);
  console.log('Structured Data:', structured_data);
  console.log('Content Optimization:', content_optimization);
  
  res.status(200).send('OK');
});
```

## Testing the Workflow

### 1. Test Webhook

Send a POST request to trigger the workflow:

```bash
curl -X POST https://your-n8n-instance.com/webhook/ai-seo-workflow \
  -H "Content-Type: application/json" \
  -d '{
    "website_url": "https://example.com",
    "api_key": "your_api_key",
    "content": "Sample content to analyze for SEO",
    "webhook_url": "https://your-results-endpoint.com/webhook"
  }'
```

### 2. Monitor Execution

- Check n8n execution log
- Verify each node completes successfully
- Check your webhook endpoint receives results

### 3. Validate Results

Expected output structure:
```json
{
  "log_analysis": "JSON with bot crawl analysis and recommendations",
  "structured_data": "JSON-LD schema markup",
  "content_optimization": "Content improvement recommendations",
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

## Troubleshooting

### Common Issues

1. **API Key Errors**
   - Verify Anthropic API key is valid
   - Check credit balance
   - Ensure key has proper permissions

2. **Webhook Timeouts**
   - Increase n8n timeout settings
   - Check server log API response time
   - Verify webhook endpoint is accessible

3. **Log Analysis Errors**
   - Ensure server logs are in standard format
   - Check log file permissions
   - Verify API endpoint returns proper data

### Debug Mode

Enable detailed logging in n8n:
1. Go to Settings → Environment Variables
2. Set `N8N_LOG_LEVEL=debug`
3. Restart n8n instance

## Customization

### Adding More AI Nodes
```json
{
  "parameters": {
    "model": "claude-3-5-sonnet-20241022",
    "prompt": "Your custom prompt here: {{ $json.input_data }}"
  },
  "type": "n8n-nodes-base.anthropic"
}
```

### Custom Schema Types

Modify the schema generator prompt to include additional schema types:
- Organization
- LocalBusiness
- Product
- Event
- Recipe

### Scheduling

Add a Cron Trigger node for automated daily/weekly analysis:
```json
{
  "parameters": {
    "rule": {
      "interval": [{"field": "cronExpression", "expression": "0 0 * * 1"}]
    }
  },
  "type": "n8n-nodes-base.cron"
}
```

## Security Notes

- Never commit API keys to version control
- Use environment variables for sensitive data
- Secure your webhook endpoints with authentication
- Regularly rotate API keys
- Monitor API usage and costs

## Support

For issues with:
- **n8n workflow**: Check n8n documentation
- **Anthropic API**: Check Anthropic API docs
- **SEO methodology**: Refer to Diggity Marketing case study