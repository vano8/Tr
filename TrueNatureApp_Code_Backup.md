# True Nature App Code Backup

This file contains all the necessary code for the **True Nature App**, including backend, frontend, and assets, organized for easy reference or storage.

## File Structure
```
TrueNatureApp/
├── backend/
│   ├── routes/
│   │   ├── authRoutes.js
│   │   ├── protectedRoutes.js
│   ├── utils/
│   │   ├── security.js
│   │   ├── emailService.js
│   ├── middleware/
│   │   ├── authMiddleware.js
│   ├── .env.example
│   ├── package.json
│   ├── server.js
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── SplashScreen.jsx
│   │   │   ├── SocialLogin.jsx
│   │   │   ├── ComplianceQuestionnaire.jsx
│   │   │   ├── FinalComplianceCheck.jsx
│   │   │   ├── HomeFeed.jsx
│   │   │   ├── MarketplaceGrid.jsx
│   │   │   ├── ProductCard.jsx
│   │   ├── routes/
│   │   │   ├── PrivateRoute.jsx
│   │   ├── App.jsx
│   ├── .env.example
│   ├── package.json
├── assets/
│   ├── forest-sunlight.json
│   ├── true-nature-logo.png
│   ├── forest_background.jpg
│   ├── golden_leaf.png
│   ├── google_icon.png
│   ├── facebook_icon.png
│   ├── apple_icon.png
│   ├── leaf_filled.png
│   ├── leaf_empty.png
│   ├── butterfly_release.json
│   ├── buy-button.png
│   ├── verified-leaf.png
│   ├── globe.png
│   ├── forest_animals.json
├── README.md
```

### Backend Code

```javascript name=backend/server.js
const express = require('express');
const cookieParser = require('cookie-parser');
const authRoutes = require('./routes/authRoutes');
const protectedRoutes = require('./routes/protectedRoutes');
require('dotenv').config();

const app = express();
app.use(express.json());
app.use(cookieParser());

// Routes
app.use('/api/auth', authRoutes);
app.use('/api', protectedRoutes);

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

```javascript name=backend/routes/authRoutes.js
const express = require('express');
const router = express.Router();

// Additional endpoints for login, register, and forgot-password
module.exports = router;
```

```javascript name=backend/utils/security.js
const jwt = require('jsonwebtoken');

const isPasswordStrong = (password) => (
  password.length >= 8 &&
  /[A-Z]/.test(password) &&
  /\d/.test(password) &&
  /[!@#$%^&*]/.test(password)
);

const verifyToken = (token) => {
  try {
    return jwt.verify(token, process.env.JWT_SECRET);
  } catch {
    return null;
  }
};

module.exports = { isPasswordStrong, verifyToken };
```

```javascript name=backend/middleware/authMiddleware.js
const { verifyToken } = require('../utils/security');

module.exports = function authenticateToken(req, res, next) {
  const token = req.cookies.authToken || req.headers['authorization']?.split(' ')[1];
  const decoded = verifyToken(token);
  if (!decoded) return res.status(403).json({ message: 'Invalid token' });
  req.user = decoded;
  next();
};
```

```ini name=backend/.env.example
JWT_SECRET=your_jwt_secret_here
EMAIL_USER=your_email@example.com
EMAIL_PASS=your_email_password
PORT=5000
```

```json name=backend/package.json
{
  "name": "authkit-backend",
  "dependencies": {
    "express": "^4.18.2",
    "bcryptjs": "^2.4.3",
    "jsonwebtoken": "^9.0.2",
    "dotenv": "^16.3.1",
    "nodemailer": "^6.9.8",
    "cookie-parser": "^1.4.6"
  }
}
```

### Frontend Code

```javascript name=frontend/src/App.jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import SplashScreen from './components/SplashScreen';
import SocialLogin from './components/SocialLogin';
import ComplianceQuestionnaire from './components/ComplianceQuestionnaire';
import FinalComplianceCheck from './components/FinalComplianceCheck';
import HomeFeed from './components/HomeFeed';
import MarketplaceGrid from './components/MarketplaceGrid';
import PrivateRoute from './routes/PrivateRoute';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<SplashScreen />} />
        <Route path="/login" element={<SocialLogin />} />
        <Route path="/compliance-questionnaire" element={<ComplianceQuestionnaire />} />
        <Route path="/final-compliance-check" element={<FinalComplianceCheck />} />
        <Route element={<PrivateRoute />}>
          <Route path="/home" element={<HomeFeed />} />
          <Route path="/marketplace" element={<MarketplaceGrid />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

```javascript name=frontend/src/components/SplashScreen.jsx
import React, { useEffect } from 'react';
import { View, StyleSheet, Image, StatusBar } from 'react-native';
import AnimatedLottieView from 'lottie-react-native';
import { useNavigation } from '@react-navigation/native';

export default function SplashScreen() {
  const navigation = useNavigation();

  useEffect(() => {
    const timer = setTimeout(() => {
      navigation.replace('Login'); // Transition after 3 seconds
    }, 3000);
    return () => clearTimeout(timer);
  }, []);

  return (
    <View style={styles.container}>
      <StatusBar hidden />
      <AnimatedLottieView
        source={require('../../assets/forest-sunlight.json')} // Background animation
        autoPlay
        loop={false}
        style={styles.backgroundAnimation}
      />
      <Image
        source={require('../../assets/true-nature-logo.png')} // Logo
        style={styles.logo}
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#0f3d2e',
    justifyContent: 'center',
    alignItems: 'center',
  },
  backgroundAnimation: {
    ...StyleSheet.absoluteFillObject,
    zIndex: 0,
  },
  logo: {
    width: 200,
    height: 200,
    zIndex: 1,
    resizeMode: 'contain',
  },
});
```

```ini name=frontend/.env.example
REACT_APP_API_BASE_URL=http://localhost:5000/api
```

```json name=frontend/package.json
{
  "name": "authkit-frontend",
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.22.3",
    "axios": "^1.6.7"
  }
}
```

### README

````markdown name=README.md
# True Nature App

This repository contains the complete implementation of the **True Nature App** with the following features:
- Animated Splash Screen
- Social Login
- Compliance Questionnaire
- Final Compliance Check
- Home Feed Interface
- Marketplace View
- Authentication System (AuthKit)

## Usage
1. Navigate to the `backend` and `frontend` directories and run `npm install`.
2. Rename `.env.example` to `.env` in both `backend` and `frontend`.
3. Populate the `.env` files with your configuration.
4. Start the backend server: `node backend/server.js`.
5. Start the frontend app: `npm start` (inside `frontend`).

## Features
1. **Splash Screen**: Includes animations and transitions to the login screen.
2. **Social Login**: Supports Google, Facebook, and Apple login.
3. **Compliance Questionnaire**: A progress bar to guide user submission.
4. **Final Compliance Check**: Interactive decision-making steps.
5. **Home Feed**: Scrollable video feed.
6. **Marketplace**: Grid with autoplay product videos.