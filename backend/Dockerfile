# Use the official Node.js 14 image as the base image
#FROM node:14
FROM node:14.15.1
# Set the working directory inside the container to /app
WORKDIR /app

# Copy both package.json and package-lock.json (if available) to the working directory
COPY backend/package*.json ./

# Copy the rest of the application files (excluding node_modules) into the container
COPY . ./

# Install the Node.js dependencies from the package.json file
#RUN npm install
RUN yarn install

# Define the default command to run the application using npm start
CMD ["npm", "start"]
