# Use Node.js 18
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy package files first for better caching
COPY package*.json ./
COPY client/package*.json ./client/

# Copy ALL client source files (before installing dependencies to ensure all files are present)
COPY client/ ./client/
# Remove node_modules and dist from copied files (they will be created/installed)
RUN rm -rf /app/client/node_modules /app/client/dist || true

# Install root dependencies
RUN npm ci --omit=dev

# Install client dependencies
WORKDIR /app/client
RUN npm ci

# Verify index.html exists before building
RUN echo "=== Checking client directory ===" && \
    ls -la /app/client/ && \
    echo "=== Checking for index.html ===" && \
    test -f /app/client/index.html || (echo "ERROR: index.html not found in /app/client/" && ls -la /app/client/ && exit 1) && \
    echo "=== index.html found ===" && \
    cat /app/client/index.html | head -5

# Verify vite.config.js exists
RUN test -f /app/client/vite.config.js || (echo "ERROR: vite.config.js not found!" && exit 1) && \
    echo "=== vite.config.js found ==="

# Verify src directory exists
RUN test -d /app/client/src || (echo "ERROR: src directory not found!" && exit 1) && \
    echo "=== src directory found ==="

# Build client from client directory
WORKDIR /app/client
RUN echo "=== Current directory: $(pwd) ===" && \
    echo "=== Files in current directory ===" && \
    ls -la && \
    echo "=== Checking index.html ===" && \
    cat index.html | head -3 && \
    echo "=== Checking vite.config.js ===" && \
    cat vite.config.js | head -5 && \
    echo "=== Running build ===" && \
    npm run build

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
