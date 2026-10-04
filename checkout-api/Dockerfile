# --- Stage 1: Build & Compile the TypeScript Application ---
FROM node:20-bookworm-slim AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

RUN npm run build --if-present


# --- Stage 2: Production Dependency Isolation ---
FROM node:20-bookworm-slim AS dependency-cleaner

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev


# --- Stage 3: Security-Hardened Runtime ---
FROM node:20-bookworm-slim AS runner

WORKDIR /app

ENV NODE_ENV=production

# Apply the latest Debian security updates
RUN apt-get update \
    && apt-get upgrade -y \
    && rm -rf /var/lib/apt/lists/*

# Copy only production dependencies and application files
COPY --from=dependency-cleaner /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/db ./db
COPY --from=builder /app/package.json ./package.json

# Remove npm tooling from the runtime image
RUN rm -rf /usr/local/lib/node_modules/npm \
           /usr/local/bin/npm \
           /usr/local/bin/npx

EXPOSE 3000

CMD ["node", "dist/index.js"]
