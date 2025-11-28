# ---- Build stage ----
FROM node:20-alpine AS build

WORKDIR /app

# Install dependencies
COPY package*.json ./
RUN npm install

# Copy source
COPY . .

# Build Angular app for production
# (same as: ng build --configuration production)
RUN npm run build -- --configuration=production

# ---- Runtime stage ----
FROM nginx:1.27-alpine

# Copy built Angular files from build stage
# IMPORTANT: replace <PROJECT_NAME> with the name in angular.json outputPath
COPY --from=build /app/dist/angular-15-crud/ /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
