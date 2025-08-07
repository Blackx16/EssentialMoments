# Essential Moments – Development Setup Guide

This project uses **Docker** to create a consistent and isolated development environment for all team members.  
You do **not** need to install Node.js or the Android SDK on your host machine.

---

## 1. Prerequisites

Before you begin, ensure you have the following software installed and running on your local machine:

- **Git** for version control  
- **Docker Desktop** for containerization

---

## 2. Environment Setup

Follow these steps to build and run the development container.

### Step 2.1: Clone the Repository

First, clone the project from our Git repository and navigate into the project directory.

```bash
# Clone the repository
git clone git@github.com:EssentialMoments/app.git

# Navigate into the project directory
cd app

# Switch to our main development branch
git checkout develop
```

---

### Step 2.2: Configure Local Secrets

The application requires API keys and other secrets to run. These are managed locally via an `.env` file, which is **never** committed to Git.

```bash
# Create a copy of the example environment file
cp .env.example .env
```

Open the new `.env` file and populate it with the necessary developer keys.

---

### Step 2.3: Launch the Docker Container

With Docker Desktop running, use `docker-compose` to build the image and start the development container.

```bash
# Build and start the container in detached mode
docker-compose up -d --build
```

> The first build may take several minutes as it downloads the base image and installs all dependencies.

---

### Step 2.4: Enter the Container

Once the container is running, access its command line (shell) to run project-specific commands.

```bash
# Get a bash shell inside the 'app' container
docker-compose exec app bash
```

All subsequent commands in this guide should be run from **inside** this container shell.

---

### Verify the Capacitor Setup

Let's initialize Capacitor for Android:

```bash
# (Inside the container)
npx cap init
npx cap add android
```

You will be prompted for the App Name and App ID. Use the following:

- **App Name**: `Essential Moments`  
- **App ID**: `com.essentialmoments.app`

> The App ID follows a reverse domain name convention. It must be unique.

This will create a new file in your project called `capacitor.config.ts`.

## Setting up files before test run:
### 1.Create the Babel Config File:
In the root of your project (/usr/src/app/), create a new file named `babel.config.js`.
```bash

# (Inside the container)
touch babel.config.js
```
### 2.Add the Configuration:
Open this new babel.config.js file on your host machine and paste the following code into it. This tells Jest to use the standard React Native preset to understand JSX.

```JavaScript
// babel.config.js
module.exports = {
  presets: ['@react-native/babel-preset'],
};
```
---

## 3. Key Project Commands

Here are the essential commands you will use during development.

---

### Running Tests

#### Let's Create Our First Test

To confirm everything is wired up, we'll create a simple "smoke test" that just checks that our main App component renders without crashing.

---

#### 1. Create a Placeholder `App.tsx` File

First, make sure you have an `App.tsx` file in the root of your project (`/usr/src/app/`).  
If you don't, create it with this minimal content:

```tsx
// /usr/src/app/App.tsx
import React from 'react';
import { SafeAreaView, Text } from 'react-native';

const App = () => {
  return (
    <SafeAreaView>
      <Text>Essential Moments</Text>
    </SafeAreaView>
  );
};

export default App;
```

---

#### 2. Create the Test File

Create a new directory called `__tests__` (with two underscores on each side).

```bash
# (Inside the container)
mkdir __tests__
```

Then create the test file inside that new directory:

```bash
# (Inside the container)
touch __tests__/App.test.tsx
```

Open `__tests__/App.test.tsx` and paste this content:

```tsx
// /usr/src/app/__tests__/App.test.tsx
import 'react-native';
import React from 'react';
import App from '../App';

// Note: test renderer must be required after react-native.
import renderer from 'react-test-renderer';

// This is the actual test block that was missing
it('renders correctly', () => {
  renderer.create(<App />);
});
```

To verify that everything is set up correctly and that the test passes, run:

```bash
npm run test
```

---

### Initializing Capacitor

If you're setting up the project for the first time, initialize Capacitor and add the native Android platform:

```bash
# 1. Initialize Capacitor configuration (only needs to be done once)
npx cap init

# 2. Add the native Android project
npx cap add android
```

---

### Stopping the Environment

When you're done with your development session, stop the container from your host machine:

```bash
docker-compose down
```

---

## 4. Git Workflow

We follow the **GitFlow branching model** to keep our codebase organized and stable.

- **main**: This branch contains production-ready, tagged releases.  
  🚫 Do **not** commit directly to this branch.

- **develop**: This is the primary development branch.  
  All feature branches are created from `develop` and merged back into it.

- **feature/<your-feature-name>**:  
  All new work must be done on a feature branch.  
  Example:

  ```bash
  git checkout -b feature/my-awesome-feature
  ```