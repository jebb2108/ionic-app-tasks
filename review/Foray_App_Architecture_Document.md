# Foray App Architecture Document

## 1. Overview

This document outlines the architectural design for the Foray app based on the migration from a vanilla HTML/CSS/JS architecture to an Ionic React-based architecture. It details the component tree, routing, state management, integration of Capacitor plugins, and iOS-specific configurations.

## 2. Current Architecture Analysis

### 2.1 Architecture Description
- **Technology Stack**: Utilizes vanilla HTML, CSS, and JavaScript.
- **Structure**: Comprised of 6 individual, static pages with basic navigation.
- **User Interactions**: Limited to standard links, lacking dynamic features.

### 2.2 Pros and Cons

#### Pros
- **Simplicity**: Straightforward for minor edits and rapid prototyping.
- **No External Dependencies**: Reduces initial page weight and load times.

#### Cons
- **Lack of Scalability**: The absence of an organized component structure hinders growth.
- **Poor Responsiveness**: Manual responsiveness requires extensive CSS updates.
- **Limited Interactivity**: A static nature restricts complex user interactions and animations.
- **No Reusable Components**: Leads to code duplication and inconsistent UI.

## 3. Comparison of Approaches

### 3.1 Vanilla HTML/CSS/JS
- **User Experience**: Lack of dynamic behavior may frustrate modern users.
- **Development Speed**: Increased time and effort for feature enhancements.
- **HIG Compliance**: Difficulties complying with iOS design guidelines.

### 3.2 Migration to Ionic React
- **Technology Stack**: Adoption of React and the Ionic Framework for mobile UI.
- **Structure**: Transition to a Single Page Application (SPA) fostering shared components, state management, and routing.

#### Pros
- **Reusable Components**: Streamlined development and better maintainability.
- **Enhanced UX**: Ability to integrate modern features seamlessly.
- **HIG Compliance**: Native-like interface adhering to iOS design principles.
- **Performance**: Improved responsiveness through React’s virtual DOM.

#### Cons
- **Learning Curve**: Teams must invest time in acquiring new skills.
- **Dependency Complexity**: Managing libraries may introduce overhead.
- **Migration Effort**: Initial time needed to refactor legacy code.

## 4. Architectural Design

### 4.1 Component Tree
The proposed component tree is structured as follows:
```
- App
  - Home
  - Login
  - Dashboard
    - Settings
    - Profile
    - Notifications
  - About
  - Error (for 404 pages)
```

### 4.2 Routing
Utilizing React Router for routing, the routes will be configured as:
```jsx
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import Home from './components/Home';
import Login from './components/Login';
import Dashboard from './components/Dashboard';
import Settings from './components/Settings';
import Profile from './components/Profile';
import Notifications from './components/Notifications';
import About from './components/About';
import Error from './components/Error';

const AppRouter = () => {
  return (
    <Router>
      <Switch>
        <Route path='/' exact component={Home} />
        <Route path='/login' component={Login} />
        <Route path='/dashboard' component={Dashboard} />
        <Route path='/settings' component={Settings} />
        <Route path='/profile' component={Profile} />
        <Route path='/notifications' component={Notifications} />
        <Route path='/about' component={About} />
        <Route component={Error} />
      </Switch>
    </Router>
  );
};

export default AppRouter;
```

### 4.3 State Management
Utilizing Zustand for lightweight state management. Example is below:
```javascript
import create from 'zustand';

const useStore = create(set => ({
  user: null,
  setUser: (user) => set({ user }),
  notifications: [],
  addNotification: (notification) => set(state => ({ notifications: [...state.notifications, notification] })),
}));

export default useStore;
```

### 4.4 Capacitor Plugins
Integration of essential Capacitor plugins:
- **Camera**: For capturing photos or videos.
- **Geolocation**: To get user location.
- **Push Notifications**: For sending notifications.
- **Biometrics**: For secure biometric authentication.
- **Secure Storage**: To securely store user credentials or sensitive data.

Sample plugin initialization:
```javascript
import { Plugins } from '@capacitor/core';
const { Camera, Geolocation } = Plugins;

const takePhoto = async () => {
  const image = await Camera.getPhoto({
    quality: 90,
    allowEditing: false,
    resultType: CameraResultType.Uri,
  });
  // Process the image...
};
```

### 4.5 Platform-Specific Configurations for iOS
- **Info.plist**: Include necessary permissions for camera, location, and notifications.
- **Launch Screen**: Ensure it adheres to iOS launch screen guidelines.
- **Styling**: Follow iOS Human Interface Guidelines for fonts, colors, and layouts.

```xml
<key>NSCameraUsageDescription</key>
<string>We need access to your camera to take photos.</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>We need your location to provide better services.</string>
<key>UIBackgroundModes</key>
<array>
  <string>fetch</string>
  <string>remote-notification</string>
</array>
```

## 5. Conclusion

Migrating the Foray app from a vanilla architecture to an Ionic React-based framework aligns with modern development practices, enhances user experience, ensures compliance with iOS design standards, and positions the application for future growth and maintainability. The proposed component structure, routing, state management, and integration of Capacitor plugins provide a comprehensive framework for a scalable application that is ready for deployment on iOS from day one. Further training for the development team and incremental migration are imperative for smooth transition and minimal disruption.