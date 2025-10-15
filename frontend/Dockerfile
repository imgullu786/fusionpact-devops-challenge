# Use lightweight Nginx image
FROM nginx:stable-alpine


# Remove default nginx static content
RUN rm -rf /usr/share/nginx/html/*

# Copy your HTML file into the web root
COPY Devops_Intern.html /usr/share/nginx/html/index.html

# Expose port 80 for web traffic
EXPOSE 80

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s CMD wget -q -O- http://localhost/ || exit 1

# Run nginx in foreground
CMD ["nginx", "-g", "daemon off;"]
