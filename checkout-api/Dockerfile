# --- Stage 1: Build & Compile the TypeScript Application ---
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build --if-present

# --- Stage 2: Production Dependency Isolation ---
FROM node:20-alpine AS dependency-cleaner
WORKDIR /app
COPY package*.json ./
# Strips out devDependencies entirely, installing only essential execution modules
RUN npm ci --only=production

# --- Stage 3: Zero-Trust Hardened Runtime ---
FROM gcr.io/distroless/nodejs20-debian12 AS runner
WORKDIR /app
ENV NODE_ENV=production

# Copy ONLY clean production modules and compiled build folders
COPY --from=dependency-cleaner /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/db ./db
COPY --from=builder /app/package.json ./package.json

EXPOSE 3000
CMD ["dist/index.js"]
