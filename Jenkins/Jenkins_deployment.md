## Add Credentials
Create the secret key
```
Profile > Developer Settings > Personal access tokens > Tokens(classic)
```
Add to the Jenkins Credentials
```
Manage Jenkins > credentials>DOmains(global) > Username with password (Password == Secret Key)
```

## Add Project Item
```
+ New Item > Freestyle project
```
## Configure the Project
```
Source Code Management > Git > 
- Repository URL and Credentials
- Branch Name Build Triggers (GitHub hook trigger for GITScm polling) 
- Specify the Command without using Sudo
```
## GitHub Webhook for the Continuous Updates between Jenkins and Github
```
GitHub Repo > Settings > Webhooks > Add webhook > 
- Payload URL = https://<jenkins-url>/github-webhook/
- Content type = application/json
- Let me select individual events = Pull requests, and pushes
```

## Build Steps
Build Steps > Execute shell
```
docker-compose down
docker-compose up -d --force-recreate --no-deps --build backend
```
## For Vite+React App Modify vite.config.ts
```
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';
import tsconfigPaths from "vite-tsconfig-paths";

// https://vitejs.dev/config/
export default defineConfig({
  resolve: {
    alias: {
      '@': path.resolve(__dirname + '/src'),
    },
  },
  plugins: [react(), tsconfigPaths()],
  server: {
    host: true,
    port: 3000,
    watch: {
      usePolling: true,
    },
  },
  // https://vitejs.dev/config/#define
  define: {
    'process.env': {},
  },
});

```
## For Nextjs Based Webapp next.config.js
```
const path = require('path');

/** @type {import('next').NextConfig} */
const nextConfig = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "**",
      },
    ],
    unoptimized: true,
  },
  reactStrictMode: true,
  swcMinify: true,
  output: "standalone",

  webpack: (config, { buildId, dev, isServer, defaultLoaders, webpack }) => {
    // Resolve aliases
    config.resolve.alias = {
      ...config.resolve.alias,
      '@': path.resolve(__dirname, './src'),
    };

    return config;
  },
};

module.exports = nextConfig;
```