# backend/Dockerfile
FROM node:20-alpine

WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm ci --only=production

# Copy source
COPY . .

# Env & port
ENV NODE_ENV=production
EXPOSE 8080

CMD ["npm", "start"]
