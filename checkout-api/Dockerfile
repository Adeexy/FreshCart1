cat << 'EOF' > checkout-api/Dockerfile
# --- Stage 1: Build the TypeScript application ---
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build --if-present

# --- Stage 2: Production-ready runtime environment ---
FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

# Install only production dependencies for a lightweight image footprint
COPY package*.json ./
RUN npm ci --only=production

# Copy compiled JavaScript code from the builder stage
COPY --from=builder /app/dist ./dist || COPY --from=builder /app/src ./src
COPY --from=builder /app/db ./db

EXPOSE 3000
CMD ["node", "dist/index.js"]
EOF
