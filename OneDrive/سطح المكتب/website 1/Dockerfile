# Use Node.js 18
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files first for better caching
COPY package*.json ./
COPY client/package*.json ./client/

# Install root dependencies
RUN npm ci --omit=dev

# Install client dependencies
WORKDIR /app/client
RUN npm ci

# Copy all client files except node_modules and dist (which are already handled)
WORKDIR /app
COPY client/ ./client/
# Remove node_modules and dist if they were copied, as we want the installed versions
RUN rm -rf /app/client/node_modules /app/client/dist || true

# Verify index.html exists before building
RUN ls -la /app/client/ && test -f /app/client/index.html || (echo "ERROR: index.html not found in /app/client/" && ls -la /app/client/ && exit 1)

# Verify vite.config.js exists
RUN test -f /app/client/vite.config.js || (echo "ERROR: vite.config.js not found!" && exit 1)

# Build client from client directory
WORKDIR /app/client
RUN npm run build

# Verify build output
RUN test -f /app/client/dist/index.html || (echo "ERROR: Build failed - dist/index.html not found!" && exit 1)

# Copy server files
WORKDIR /app
COPY server/ ./server/

# Back to root
WORKDIR /app

# Expose port
EXPOSE 3000

# Start server
CMD ["npm", "start"]
