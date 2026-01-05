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

# Copy ALL remaining files
WORKDIR /app
COPY . .

# Verify index.html exists before building
RUN ls -la /app/client/ && test -f /app/client/index.html || (echo "ERROR: index.html not found in /app/client/" && ls -la /app/client/ && exit 1)

# Build client from client directory
WORKDIR /app/client
RUN npm run build

# Verify build output
RUN test -f /app/client/dist/index.html || (echo "ERROR: Build failed - dist/index.html not found!" && exit 1)

# Back to root
WORKDIR /app

# Expose port
EXPOSE 3000

# Start server
CMD ["npm", "start"]
