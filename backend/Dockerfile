# Use Node.js LTS as the base image
FROM node:18

# Set the working directory inside the container
WORKDIR /app

# Copy the package.json and package-lock.json
COPY package*.json ./

# Install the Node.js dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose the port that the backend will run on
EXPOSE 5000

# Command to run the backend
CMD ["node", "index.js"]
