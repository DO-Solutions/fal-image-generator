# fal-image-generator
 Text to Image generation app for you that uses Fal.ai's SDXL model via DigitalOcean's serverless inference API.

# AI Image Generator - Fal.ai SDXL on DigitalOcean

A web application that generates images using Fal.ai's Stable Diffusion XL model via DigitalOcean's Serverless Inference API.

## Features

- 🎨 Generate high-quality images from text prompts
- ⚡ Fast SDXL model for quick generation
- 🎛️ Advanced controls (inference steps, guidance scale)
- 📥 Download generated images
- 🌐 Responsive web interface
- ☁️ Deploy to DigitalOcean App Platform

## Prerequisites

1. **DigitalOcean Account** with access to Gradient AI Platform
2. **Opt-in to Fal.ai models** in DigitalOcean console:
   - Go to: https://cloud.digitalocean.com/account/feature-preview?feature=fal-models
   - Enable the feature preview
3. **API Access Token** from DigitalOcean

## Local Development Setup

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd fal-ai-image-generator
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment variables

Create a `.env` file in the root directory:

```bash
cp .env.example .env
```

Edit `.env` and add your DigitalOcean API key:

```
MODEL_ACCESS_KEY=your_digitalocean_api_key_here
PORT=3000
```

To get your API key:
- Go to: https://cloud.digitalocean.com/account/api/tokens
- Click "Generate New Token"
- Give it a name and select appropriate scopes
- Copy the token and paste it in your `.env` file

### 4. Run the application

```bash
# Development mode (with auto-restart)
npm run dev

# Production mode
npm start
```

Open your browser and navigate to: `http://localhost:3000`

## Project Structure

```
fal-ai-image-generator/
├── server.js           # Express backend server
├── public/
│   └── index.html      # Frontend interface
├── package.json        # Node.js dependencies
├── .env.example        # Environment variables template
├── .gitignore         # Git ignore file
└── README.md          # This file
```

## Deploying to DigitalOcean App Platform

### Method 1: Using GitHub

1. **Push your code to GitHub**

```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin <your-github-repo-url>
git push -u origin main
```

2. **Create a new App on DigitalOcean**
   - Go to: https://cloud.digitalocean.com/apps
   - Click "Create App"
   - Select "GitHub" as the source
   - Choose your repository
   - DigitalOcean will auto-detect the Node.js app

3. **Configure the App**
   - **Name**: `fal-ai-image-generator`
   - **Region**: Choose closest to you
   - **Plan**: Basic ($5/month minimum)
   - **Environment Variables**: Add `MODEL_ACCESS_KEY`
   - **Build Command**: `npm install`
   - **Run Command**: `npm start`
   - **HTTP Port**: 3000

4. **Add Environment Variable**
   - In the App settings, go to "Settings" → "App-Level Environment Variables"
   - Add: `MODEL_ACCESS_KEY` = `your_digitalocean_api_key`

5. **Deploy**
   - Click "Create Resources"
   - Wait for deployment to complete
   - Your app will be available at: `https://your-app-name.ondigitalocean.app`

### Method 2: Using DigitalOcean CLI (doctl)

```bash
# Install doctl
# macOS: brew install doctl
# Other: https://docs.digitalocean.com/reference/doctl/how-to/install/

# Authenticate
doctl auth init

# Create app.yaml configuration file
cat > app.yaml << EOF
name: fal-ai-image-generator
region: nyc
services:
- name: web
  github:
    repo: your-username/your-repo
    branch: main
  build_command: npm install
  run_command: npm start
  http_port: 3000
  environment_slug: node-js
  instance_count: 1
  instance_size_slug: basic-xxs
  envs:
  - key: MODEL_ACCESS_KEY
    value: your_digitalocean_api_key
    type: SECRET
EOF

# Deploy
doctl apps create --spec app.yaml
```

## API Endpoints

### POST /api/generate
Generate an image from a text prompt.

**Request Body:**
```json
{
  "prompt": "A futuristic city at sunset",
  "num_inference_steps": 4,
  "guidance_scale": 3.5,
  "num_images": 1
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "images": [
      {
        "url": "https://...",
        "width": 1024,
        "height": 768
      }
    ]
  }
}
```

### GET /health
Health check endpoint.

**Response:**
```json
{
  "status": "healthy",
  "timestamp": "2025-01-01T00:00:00.000Z"
}
```

## Configuration Options

### Advanced Parameters

- **num_inference_steps** (1-8): Higher values = better quality but slower (default: 4)
- **guidance_scale** (1-10): How closely to follow the prompt (default: 3.5)
- **output_format**: Image aspect ratio (default: landscape_4_3)
- **num_images** (1-4): Number of images to generate (default: 1)

## Troubleshooting

### "MODEL_ACCESS_KEY is not configured"
- Make sure you've created a `.env` file with your API key
- Check that the environment variable is set correctly in DigitalOcean App Platform

### "Request timed out"
- The model might be cold-starting (first request can take longer)
- Try again in a few seconds
- Check DigitalOcean's status page for any API issues

### "Image generation failed"
- Check that you've opted into the Fal.ai models feature preview
- Verify your API key has the correct permissions
- Check the server logs for detailed error messages

### Port Issues
- DigitalOcean App Platform automatically sets the PORT environment variable
- The app is configured to use `process.env.PORT` or default to 3000

## Cost Estimation

- **DigitalOcean App Platform**: $5-12/month (Basic plan)
- **API Calls**: Check current pricing at https://www.digitalocean.com/pricing/gradient-ai-platform
- **First-time users**: May qualify for credits

## Resources

- [DigitalOcean Gradient AI Platform Docs](https://docs.digitalocean.com/products/gradient-ai-platform/)
- [Serverless Inference API](https://docs.digitalocean.com/products/gradient-ai-platform/how-to/use-serverless-inference/)
- [Fal.ai Documentation](https://fal.ai/docs)
- [Blog Post: Fal.ai on DigitalOcean](https://www.digitalocean.com/blog/fal-ai-image-models-gradient-ai-platform)

## License

MIT

## Support

For issues and questions:
- GitHub Issues: Create an issue in this repository
- DigitalOcean Support: https://docs.digitalocean.com/support/
- Community: https://www.digitalocean.com/community/
