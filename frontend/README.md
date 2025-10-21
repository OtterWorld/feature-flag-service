# Feature Flag Service - Frontend

React + TypeScript + Vite application for the Feature Flag Service.

## Development

### Prerequisites
- Node.js 20+
- npm

### Local Development (without Docker)

```bash
# Install dependencies
npm install

# Start development server (runs on http://localhost:3000)
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Type checking
npm run type-check

# Lint
npm run lint
```

### Docker Development

```bash
# Build development image
docker build --target development -t feature-flag-frontend:dev .

# Run development container
docker run -p 3000:3000 -v $(pwd):/app feature-flag-frontend:dev

# Build production image
docker build --target production -t feature-flag-frontend:prod .

# Run production container
docker run -p 80:80 feature-flag-frontend:prod
```

### Using Docker Compose (Recommended for local development)

See the root `docker-compose.yml` for running the full stack.

```bash
# From project root
docker-compose up
```

## Project Structure

```
frontend/
├── public/              # Static assets
├── src/
│   ├── assets/         # Images, fonts, etc.
│   ├── components/     # Reusable React components
│   ├── contexts/       # React contexts
│   ├── hooks/          # Custom React hooks
│   ├── pages/          # Page components
│   ├── services/       # API services
│   ├── types/          # TypeScript type definitions
│   ├── utils/          # Utility functions
│   ├── App.tsx         # Main App component
│   ├── App.css         # App styles
│   ├── main.tsx        # Application entry point
│   └── index.css       # Global styles
├── Dockerfile          # Multi-stage Docker build
├── nginx.conf          # Nginx configuration for production
├── package.json        # Dependencies and scripts
├── tsconfig.json       # TypeScript configuration
└── vite.config.ts      # Vite configuration
```

## API Integration

The application is configured to proxy `/api` requests to the backend service:
- **Development**: Vite dev server proxies to `http://localhost:8080`
- **Production**: Nginx proxies to backend service

## Deployment

This frontend can be deployed independently:

1. **Build the production image:**
   ```bash
   docker build --target production -t feature-flag-frontend .
   ```

2. **Run the container:**
   ```bash
   docker run -p 80:80 feature-flag-frontend
   ```

For CI/CD pipelines, use the production stage to create optimized builds.
