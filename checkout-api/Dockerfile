# --- Stage 1: Build the application assets ---
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build --if-present

# --- Stage 2: Hardened Production-ready runtime environment ---
FROM gcr.io/distroless/nodejs20-debian12 AS runner
WORKDIR /app
ENV NODE_ENV=production

# Copy all application assets cleanly from the builder layer
COPY --from=builder /app ./

EXPOSE 3000
CMD ["src/index.js"]
