# Use a Long-Term Support version of Node.js as the base image
FROM node:18-bullseye

# Set environment variables for the Android SDK
ENV ANDROID_SDK_ROOT /opt/android-sdk
ENV PATH $PATH:$ANDROID_SDK_ROOT/cmdline-tools/latest/bin:$ANDROID_SDK_ROOT/platform-tools

# Install system dependencies required for Android SDK and Capacitor
RUN apt-get update && apt-get install -y --no-install-recommends \
    openjdk-11-jdk \
    unzip \
    wget \
    && rm -rf /var/lib/apt/lists/*

# Download and install Android command-line tools
RUN mkdir -p /opt/android-sdk && \
    wget -q https://dl.google.com/android/repository/commandlinetools-linux-8512546_latest.zip -O /tmp/cmd-tools.zip && \
    unzip -q /tmp/cmd-tools.zip -d /opt/android-sdk/cmdline-tools && \
    mv /opt/android-sdk/cmdline-tools/cmdline-tools /opt/android-sdk/cmdline-tools/latest && \
    rm /tmp/cmd-tools.zip

# Accept SDK licenses and install platform-tools and the required SDK platform
RUN yes | sdkmanager --licenses && \
    sdkmanager "platform-tools" "platforms;android-33"

# --- FIX IS HERE ---
# Install Capacitor CLI globally as root, BEFORE switching user
RUN npm install -g @capacitor/cli

# Set up the working directory
WORKDIR /usr/src/app

# Now, create the non-root user and give it ownership of the app directory
RUN chown -R node:node /usr/src/app

# Switch to the non-root user for all subsequent commands
USER node

# Copy package files and install dependencies as the 'node' user
COPY --chown=node:node package*.json ./
RUN npm install

# Copy the rest of the application code
COPY --chown=node:node . .

# Expose the Metro bundler port
EXPOSE 8081