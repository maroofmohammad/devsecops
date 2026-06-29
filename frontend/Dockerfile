# Stage 1 — build the app into static files
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci --legacy-peer-deps
COPY . .
RUN npm run build

# Stage 2 — small image that only serves the build
FROM node:20-alpine
WORKDIR /app
RUN apk add --no-cache dumb-init=1.2.5-r3 && npm install -g vite@5 && npm cache clean --force
RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001
COPY --from=build /app/dist ./dist
COPY vite.preview.config.js ./vite.config.js
RUN chown -R nodejs:nodejs /app
USER nodejs
EXPOSE 4173
ENTRYPOINT ["/usr/sbin/dumb-init", "--"]
CMD ["vite", "preview", "--host", "0.0.0.0", "--port", "4173"]
