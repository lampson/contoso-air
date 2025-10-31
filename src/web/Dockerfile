# Multi-stage Dockerfile: build Next.js at image build time, run only 'next start' in runtime image

# Stage 1: Builder
FROM node:22-alpine AS builder
WORKDIR /usr/src/app

# Install dependencies for build
COPY package*.json ./
# If you use pnpm or yarn, replace the next line accordingly
RUN npm ci --production=false

# Copy source and build
COPY . .
ENV NODE_ENV=production
# Disable Next telemetry during build
ARG NEXT_TELEMETRY_DISABLED=1
ENV NEXT_TELEMETRY_DISABLED=$NEXT_TELEMETRY_DISABLED

# Run the Next build (produces .next)
RUN npm run build

# Stage 2: Runner (smaller runtime image)
FROM node:22-alpine AS runner
WORKDIR /usr/src/app

ENV NODE_ENV=production
ENV PORT=3000
ENV NEXT_TELEMETRY_DISABLED=1

# Copy only the runtime artifacts from the builder
COPY --from=builder /usr/src/app/.next ./.next
COPY --from=builder /usr/src/app/public ./public
COPY --from=builder /usr/src/app/node_modules ./node_modules
COPY --from=builder /usr/src/app/package.json ./package.json

EXPOSE 3000

# Run the prebuilt app. The start script should NOT run the build.
CMD ["npm", "run", "start"]
