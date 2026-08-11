# Stage 1: Build
FROM dhi.io/node:26-dev AS build

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Runtime
FROM dhi.io/node:26

WORKDIR /app

# Create non-root user
RUN groupadd -r appuser && useradd -r -g appuser appuser

# Copy only built artifacts from builder
COPY --from=build /app/dist ./dist
COPY --from=build /app/node_modules ./node_modules
COPY package.json ./

# Fix ownership
RUN chown -R appuser:appuser /app

# Switch to non-root user
USER appuser

EXPOSE 4173

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3000', (r) => {if (r.statusCode!==200) throw new Error(r.statusCode)})"

CMD ["node", "dist/server.js"]
