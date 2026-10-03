# --- Stage 1: Build the TypeScript application ---
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build --if-present

# --- Stage 3: Production-ready runtime environment ---
FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

COPY package*.json ./
RUN npm ci --only=production

COPY --from=builder /app/dist ./dist || COPY --from=builder /app/src ./src
COPY --from=builder /app/db ./db

EXPOSE 3000
CMD ["node", "dist/index.js"]

