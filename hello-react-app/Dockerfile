# ---- Build Stage ----
    FROM node:20-alpine AS builder

    # Set working directory
    WORKDIR /app
    
    # Copy package.json and package-lock.json (or yarn.lock)
    COPY package*.json ./
    
    # Install dependencies
    RUN npm install --frozen-lockfile
    
    # Copy all project files
    COPY . .
    
    # Build the Next.js app
    RUN npm run build
    
    # ---- Run Stage ----
    FROM node:20-alpine AS runner
    
    # Set working directory
    WORKDIR /app

    # Create a non-root user with a UID in the range 10000-20000
    RUN addgroup -S appgroup && adduser -S -u 10001 -G appgroup appuser

    # Switch to the non-root user
    USER 10001
    
    # Copy only the necessary files from the build stage
    COPY --from=builder /app/package.json ./
    COPY --from=builder /app/node_modules ./node_modules
    COPY --from=builder /app/.next ./.next
    COPY --from=builder /app/public ./public
    
    # Expose the port (default Next.js port)
    EXPOSE 3000
    
    # Run Next.js in production mode
    CMD ["npm", "run", "start"]
    