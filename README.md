# TechTalent Website

A modern website for TechTalent built with [Next.js](https://nextjs.org), featuring both client and admin interfaces with dual authentication modes: simple development authentication and Microsoft Entra ID for production.

## Getting Started

First, clone the repository and install dependencies:

```bash
git clone git@github.com:talamitay/techtalent_website_react.git
cd techtalent_website_react
npm install
```

Then, run the development server:

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) with your browser to see the result.

## Authentication System

The website includes an admin area with two authentication modes:

### Development Mode (Default)

Simple username/password authentication for development and testing:
- Username: `admin`
- Password: `1234`

### Production Mode (Microsoft Entra ID)

For production environments, the site can use Microsoft Entra ID (formerly Azure AD) for secure authentication:

1. Register an application in the Microsoft Entra ID portal
2. Configure the following environment variables:
   ```
   NEXT_PUBLIC_AUTH_MODE=production
   NEXT_PUBLIC_ENTRA_CLIENT_ID=your-client-id
   NEXT_PUBLIC_ENTRA_TENANT_ID=your-tenant-id
   NEXT_PUBLIC_ENTRA_REDIRECT_URI=your-redirect-uri
   ```

### Switching Authentication Modes

The project includes a utility script to toggle between authentication modes for testing:

```bash
node scripts/toggle-auth-mode.js
```

**Note:** Restart the development server after toggling for changes to take effect.

## Deploying to GitHub Pages

### Development Mode Deployment (For Testing)

To deploy the site to GitHub Pages in development mode (with username/password login):

1. Make sure you're in development authentication mode:
   ```bash
   # Check current mode (should be "dev")
   grep -o 'MODE: process.env.NEXT_PUBLIC_AUTH_MODE || "dev"' app/lib/config.ts
   
   # If needed, toggle to development mode
   node scripts/toggle-auth-mode.js
   ```

2. Deploy to GitHub Pages:
   ```bash
   NODE_ENV=production npm run deploy:new
   ```

The script:
1. Builds the Next.js application with static export
2. Fixes asset paths for GitHub Pages
3. Copies the build to the public repository
4. Commits and pushes the changes to GitHub

The deployed site will be available at: `https://talamitay.github.io/techtalent-website-public/`

### Production Mode Deployment

To deploy with Microsoft Entra ID authentication:

1. Switch to production authentication mode:
   ```bash
   # Toggle to production mode if currently in dev mode
   node scripts/toggle-auth-mode.js
   ```

2. Create an environment file with Microsoft Entra ID credentials:
   ```bash
   # Create .env.production file
   echo "NEXT_PUBLIC_AUTH_MODE=production" > .env.production
   echo "NEXT_PUBLIC_ENTRA_CLIENT_ID=your-client-id" >> .env.production
   echo "NEXT_PUBLIC_ENTRA_TENANT_ID=your-tenant-id" >> .env.production
   echo "NEXT_PUBLIC_ENTRA_REDIRECT_URI=https://your-domain.com/admin/login" >> .env.production
   ```

3. Deploy to GitHub Pages with production environment:
   ```bash
   NODE_ENV=production npm run deploy:new
   ```

## Project Structure

- `/app`: Main application code (Next.js App Router)
  - `/admin`: Admin dashboard and management
  - `/components`: Reusable UI components
  - `/contexts`: React contexts including auth provider
  - `/lib`: Utilities and services
    - `clientDataService.ts`: Client-side data handling
    - `config.ts`: Configuration including auth settings
    - `msalService.ts`: Microsoft authentication service
- `/public`: Static assets
- `/scripts`: Utility scripts including deployment helpers

## Updating Admin Credentials

To update the development mode admin credentials:

1. Generate hash values:
   ```bash
   # For username hash (using MD5)
   node -e "console.log(require('crypto').createHash('md5').update('YOUR_NEW_USERNAME').digest('hex'))"

   # For password hash (using SHA1)
   node -e "console.log(require('crypto').createHash('sha1').update('YOUR_NEW_PASSWORD').digest('hex'))"
   ```

2. Update the `ADMIN_CREDENTIALS` object in `app/lib/config.ts`:
   ```typescript
   export const ADMIN_CREDENTIALS = {
     USERNAME_HASH: "your_generated_username_hash",
     PASSWORD_HASH: "your_generated_password_hash",
   };
   ```

## Security Notes

- Development mode authentication is intended for testing only
- For production environments, use Microsoft Entra ID authentication
- Ensure all sensitive information is stored in environment variables
- The application uses client-side authentication with fallbacks for cookies and localStorage
