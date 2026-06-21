# Design Concept Document for Dicty Mobile App

## Task Overview

This document provides a detailed analysis of design concepts for the Dicty mobile application. Dicty aims to facilitate effective communication and data sharing among its users, specifically targeting professional networks. The document evaluates architecture, user experience (UX) patterns, compliance with iOS Human Interface Guidelines (HIG), and technical feasibility to recommend the optimal design approach.

## Target Audience

The target audience for Dicty includes professionals across various sectors who require a seamless method for communication and data sharing. Typical user personas include:

- **The Networker**: Professionals seeking to expand their contacts and collaborate.
- **The Data Sharer**: Users who prioritize efficient sharing of data and resources with minimal friction.
- **The Business Analyst**: Individuals focused on gathering insights and feedback from their networks.

## Design Concept Evaluation

### Concept 1: Chat and Data Sharing Dashboard

#### Architecture
- **Client-Server Model**: Utilizes REST API for real-time data sharing and chat functionalities.
- **Cloud Storage**: Integration with cloud services for data storage.

#### UX Patterns
- **Dashboard Layout**: Centralized dashboard featuring quick access to chat and data-sharing functionalities.
- **Persistent Navigation Bar**: Ensures easy access to essential features throughout the app.
- **Floating Action Button**: For quick data uploads and messages.

#### Compliance with iOS HIG
- Follows the iOS HIG for navigation, ensuring a clean interface and recognizable patterns. 
- Uses standard iOS elements like the navigation bar and buttons that are intuitive and familiar to users.

#### Pros
- All-in-one platform simplifies user flows.
- Reduces cognitive load with familiar UI.

#### Cons
- May overwhelm users with too much information at once.

### Concept 2: Modular Features with Tab Navigation

#### Architecture
- **Microservices Architecture**: Each module operates independently but communicates through APIs, enhancing scalability.
- **Local Database**: For offline access to previously shared data.

#### UX Patterns
- **Tab Bar Navigation**: Enhanced categorization of features (e.g., Chat, Data, Insights).
- **Modular Screens**: Each tab hosts a different feature with focused content.

#### Compliance with iOS HIG
- Leverages the tab bar as recommended by iOS HIG for multi-section navigation.
- Adheres to UIKit components for modular interfaces.

#### Pros
- Focused features help prevent information overload.
- Modular design allows future expandability.

#### Cons
- Users may need multiple taps to access some functionalities.

### Concept 3: Single Function First Approach

#### Architecture
- **Single-Page Application (SPA)**: Built to prioritize one primary function (e.g., sharing) before accessing secondary features.
- **RESTful Services**: For data fetching and sharing based on user interactions.

#### UX Patterns
- **Onboarding Walkthrough**: Introduces users to key features steadily and gradually.
- **Minimalistic Design**: Prioritizes key actions with a highly curated interface.

#### Compliance with iOS HIG
- The design leverages gestures and navigation patterns that align with iOS norms, ensuring user-friendly onboarding.

#### Pros
- Reduces initial complexity for new users.
- Encourages engagement by limiting choices at first.

#### Cons
- May frustrate power users seeking comprehensive functionality immediately.

## Recommendation

After analyzing the three concepts against user needs, target audience requirements, iOS platform standards, and technical feasibility, **Concept 2: Modular Features with Tab Navigation** stands out as the optimal approach for the following reasons:

- **User-Centric Design**: The tabbed navigation aligns with the expectations of users familiar with mobile apps, providing clear access to different functionalities without overwhelming them.
- **Scalability**: Using a microservices architecture ensures the app can grow organically, adding new features without disrupting existing functionality.
- **Focus on Understanding**: Users engage more with well-defined sections, making it easier to learn and adapt to the app.

## Next Steps

1. **Wireframing and Prototyping**: Develop low-fidelity wireframes to layout the tab structure and user flows.
2. **User Testing**: Conduct user testing sessions to validate the design direction and iterate based on feedback.
3. **Aligning with Development Team**: Prepare the finalized design documentation, including specifications for each component, to facilitate a smooth development handoff to architects.

---

By utilizing these insights and proposed frameworks, the Dicty application is poised to successfully meet the needs of its users while adhering to the standards set by the iOS ecosystem.

----------

# Architecture Document for Dicty Mobile App

## Overview

This document outlines the proposed architecture for the Dicty mobile app, focusing on the implementation of audio pronunciation features using Capacitor TextToSpeech or Native Audio. The architecture is designed to be scalable and maintainable while ensuring a smooth user experience aligned with iOS Human Interface Guidelines (HIG). 

## Component Tree

Below is the proposed component tree for the Dicty app, specifically focusing on the modular features with tab navigation:

```
App (IonicRoot)
|
|-- Navigation (React Router)
|   |
|   |-- Home (TabNavigation)
|   |    |
|   |    |-- ChatTab
|   |    |    |-- ChatList
|   |    |    |-- ChatDetail
|   |    |
|   |    |-- DataTab
|   |    |    |-- DataList
|   |    |    |-- DataDetail
|   |    |
|   |    |-- InsightsTab
|   |         |-- InsightsList
|   |         |-- InsightDetail
|   |
|   |-- Settings
|       |-- UserProfile
|       |-- Preferences
|
|-- AudioPronunciationManager
|   |-- PronunciationButton
|   |-- PronunciationPlayer
|
|-- Global State (Redux/Zustand)
|   |-- ChatState
|   |-- DataState
|   |-- InsightsState
|   |-- AudioState (For managing audio-related settings)
|
|-- Footer (for additional actions)
```

## Routing

Utilizing React Router for handling navigation between different tabs and screens:

```javascript
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import Home from './Home';
import Settings from './Settings';
...

function App() {
    return (
        <Router>
            <Switch>
                <Route path="/settings" component={Settings} />
                <Route path="/" component={Home} />
            </Switch>
        </Router>
    );
}
```

## State Management

The application will utilize Zustand as the state management solution for a simpler approach that does not require boilerplate code like Redux. The global store will manage different states required throughout the app.

### Zustand Store Structure:

```javascript
import create from 'zustand';

const useStore = create((set) => ({
    chat: [],
    setChat: (newChat) => set({ chat: newChat }),
    data: [],
    setData: (newData) => set({ data: newData }),
    insights: [],
    setInsights: (newInsights) => set({ insights: newInsights }),
    audioSettings: {
        isPlaying: false,
        currentAudio: null
    },
    setAudioSettings: (settings) => set((state) => ({
        audioSettings: { ...state.audioSettings, ...settings }
    }))
}));
```

## Capacitor Plugins

For audio pronunciation features, the app will integrate one of the following Capacitor plugins:

### Option 1: Capacitor TextToSpeech
- **Installation**: 
```bash
npm install @capacitor-community/text-to-speech
npx cap sync
```
- **Usage**:
```javascript
import { TextToSpeech } from '@capacitor-community/text-to-speech';

async function pronounceText(text) {
    await TextToSpeech.speak({
        text: text,
        lang: 'en-US',
        rate: 1.0
    });
}
```

### Option 2: Native Audio (for audio playback)
- **Installation**:
```bash
npm install @ionic-native/native-audio
npm install cordova-plugin-native-audio
npx cap sync
```
- **Usage**:
```javascript
import { NativeAudio } from '@ionic-native/native-audio';
...
NativeAudio.preloadSimple('audioId', 'path/to/audio/file.mp3').then(() => {
    NativeAudio.play('audioId');
});
```

## iOS Specific Configurations

- Ensure the application follows Apple’s HIG for navigation. Ensure usage of UIKit components like `UITabBar` for the tab navigation.
- Handle necessary permissions for text-to-speech capabilities. Update `Info.plist` to include:
```xml
<key>NSSpeechRecognitionUsageDescription</key>
<string>This app requires access to speech recognition to provide audio pronunciation.</string>
```

- Ensure all UI components are responsive and adapt to various iOS screen sizes.
- Use Objective-C or Swift bridging if necessary for advanced functionalities that Capacitor plugins may not cover by default.

## Conclusion

This architecture is structured to create a scalable and maintainable Ionic React application while providing a seamless user experience. With the focus on modular features, an efficient state management system, and robust audio functionalities using Capacitor plugins, Dicty is poised to serve its target audience effectively. The proposed design ensures that the app can evolve without degrading performance or user experience, adhering to best practices from both a technical and UX standpoint.

----------

- [x] **Тёмная тема** — работает, не ломает UI