# Complete Guide: React App with Cloudflare Pages

## Part 1: Initial Setup

### 1. Setting up Cloudflare Account
1. Visit https://www.cloudflare.com/
2. Click "Sign Up" in top right corner
3. Enter email and create password
4. Verify email via link
5. Access dashboard and locate "Pages" in sidebar

### 2. Setting up GitHub Account
1. Visit https://github.com/
2. Click "Sign Up"
3. Complete account creation:
   - Enter email
   - Create password
   - Choose username
   - Complete verification

### 3. Setting up Git Locally
```bash
# Windows: Download from https://git-scm.com/download/win
# Mac
brew install git
# Linux
sudo apt-get install git

# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### 4. Create GitHub Repository
1. Click "+" icon in GitHub
2. Select "New repository"
3. Configure:
   - Name: my-react-app
   - Description: React application using Cloudflare Pages
   - Visibility: Public
   - Don't initialize with any files
4. Click "Create repository"

## Part 2: Project Creation and Development

### 1. Create React Project
```bash
# Create new project
npm create cloudflare@latest -- my-react-app --framework=react

# During setup, choose:
# - TypeScript: Yes
# - ESLint: Yes
# - Prettier: Yes
# - Git initialization: Yes
# - Immediate deployment: No
```

### 2. Project Setup
```bash
# Navigate to project
cd my-react-app

# Connect to GitHub repository
git remote add origin https://github.com/your-username/my-react-app.git
git branch -M main
git push -u origin main
```

### 3. Configure Cloudflare Pages
1. Go to Cloudflare Dashboard
2. Navigate: Workers & Pages > Create application > Pages
3. Click "Connect to Git"
4. Select your repository
5. Configure build settings:
```
Production branch: main
Build command: npm run build
Build directory: dist
```
6. Click "Save and Deploy"

### 4. Initial Development
```tsx
// src/App.tsx
import { useState } from 'react'
import './App.css'

function App() {
  const [count, setCount] = useState(0)

  return (
    <>
      <h1>My React App with Cloudflare Pages</h1>
      <div className="card">
        <button onClick={() => setCount((count) => count + 1)}>
          Count is {count}
        </button>
        <p>Edit src/App.tsx to test auto-deployment</p>
      </div>
    </>
  )
}

export default App
```

### 5. Development Workflow
```bash
# Start local development
npm run dev

# Make and test changes locally at http://localhost:5173

# Commit and deploy changes
git add .
git commit -m "Update application content"
git push
```

### 6. Verify Deployment
1. Visit Cloudflare Pages dashboard
2. Monitor deployment progress
3. Visit your `.pages.dev` URL
4. Verify changes are live

## Part 3: Ongoing Development

### Local Development
```bash
# Start development server
npm run dev

# Install additional dependencies as needed
npm install package-name
```

### Deployment
```bash
# Commit changes
git add .
git commit -m "Description of changes"
git push
```

### Monitoring
1. Check Cloudflare Pages dashboard for:
   - Build status
   - Deployment logs
   - Analytics
   - Custom domain settings
   - Environment variables

### Important Notes
- Each push to main triggers automatic deployment
- Build logs available in Cloudflare dashboard
- Preview deployments available for pull requests
- Custom domains can be configured in Pages settings
- Environment variables can be set in Pages settings

### Troubleshooting
- Check build logs for errors
- Verify build command and directory settings
- Ensure all dependencies are properly installed
- Check for TypeScript errors before deployment

This guide provides a complete workflow from initial setup through ongoing development and deployment. Remember to commit frequently and always test locally before pushing changes. It may also be necessary to create a branch for staging changes before deployment.
