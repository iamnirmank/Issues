## docker-compose.yml for Django App
```
version: '3.3'
services:
  backend:
    build: .
    volumes:
      - ./media:/app/media
    ports:
      - '8001:8001'
```
## dockerfile for Django App
```
# Use the official Python image
FROM python:3.11-alpine

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

# Set the working directory
WORKDIR /app

# Install system dependencies
RUN apk update \
    && apk add --no-cache gcc musl-dev libffi-dev postgresql-dev

# Install Python dependencies
COPY requirements.txt .
RUN pip install --upgrade pip \
    && pip install -r requirements.txt

# Copy the application code
COPY . .

# Create a directory for media files
RUN mkdir -p /app/media

# Collect static files
RUN python manage.py collectstatic --noinput

# Apply Django migrations
RUN python manage.py migrate

# Expose the port that Daphne will run on
EXPOSE 8001

# Start the application using Daphne
CMD ["daphne", "-b", "0.0.0.0", "-p", "8001", "capitech.asgi:application"] 
```
## docker-compose.yml for Nextjs or React App
```
version: '3.3'
services:
  backend:
    build: .
    ports:
      - '3000:3000'
```
## dockerfile for React App
```
FROM node:20-alpine3.17
WORKDIR /app
COPY package.json .
RUN npm i
COPY . . 
EXPOSE 3000
CMD ["npm", "run", "dev"]
```
## dockerfile for Nextjs App
```
# Use the official Node.js image with version 18.19.0 and Alpine Linux 3.17
FROM node:18.19.0-alpine3.17

# Set the working directory
WORKDIR /app

# Copy package.json and package-lock.json (if available)
COPY package.json package-lock.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Build the Next.js application
RUN npm run build

# Expose the port the app runs on
EXPOSE 3001

# Start the application
CMD ["npm", "run", "dev"]
```