FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci --silent
COPY . .
RUN npm run build -- --prod || npm run build

# Stage 2: production stage
FROM nginx:stable-alpine
RUN rm -rf /usr/share/nginx/html/*
COPY --from=build --chown=nginx:nginx /app/dist/ /usr/share/nginx/html/
RUN chmod -R a-w /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
