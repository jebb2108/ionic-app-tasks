# Design Concept Document for Dicty

## Project Overview
Dicty is a language learning application focused on enhancing vocabulary acquisition through interactive, user-friendly features such as word cards, examples, transcripts, and audio pronunciations. Our goal is to redesign the word card to improve UX while ensuring compliance with iOS Human Interface Guidelines (HIG).

## Tasks Overview
1. **Redesign Word Card**: The card will display word definitions, examples, phonetic transcription, and audio playback options.
2. **iOS HIG Compliance**: Ensure that the proposed design adheres to iOS's design principles, ensuring optimal usability and accessibility.

## Design Concepts for Word Card

### Concept 1: Classic Layout

#### Description
This concept uses a straightforward design incorporating the word, its transcription, audio icon, and examples in a vertical stack.

#### Components
- **Word**: Bold, large font at the top center.
- **Transcription**: Smaller font directly below the word in a stylized manner.
- **Audio**: An audio playback icon to the right of the transcription.
- **Examples**: A block of text below the transcription.

#### Pros
- Clear hierarchy helps users easily identify information.
- Established design language, familiar to users.
  
#### Cons
- May feel cluttered with more examples or additional information.
- Limited creativity in visual presentation.

### Concept 2: Card Flip Interaction

#### Description
A more interactive approach where the user can tap the card to flip and view examples and audio options.

#### Components
- **Front Side**: Word and transcription visible.
- **Back Side**: Audio playback, examples, and additional definitions.

#### Pros
- Engaging and interactive experience for users.
- Reduces visual clutter on the front by separating information.

#### Cons
- May confuse users unfamiliar with card flip interactions.
- Requires additional animations, which could impact performance on lower-end devices.

### Concept 3: Modular Design

#### Description
This design emphasizes modular sections allowing users to customize how they interact with word details.

#### Components
- **Top Section**: Word and transcription with large font.
- **Audio Section**: Centralized audio playback that expands when tapped, showing length and playback controls.
- **Example Section**: List format allows for quick browsing of multiple examples.

#### Pros
- Clean, modern look that feels dynamic.
- Users can quickly interact with each section without feeling overwhelmed.

#### Cons
- May require additional onboarding to familiarize users with modular designs.
- Higher potential for inconsistent UI if modular sections are not well aligned.

## Evaluation Against iOS HIG

### Color Scheme
- Ensure use of appropriate iOS color palettes to ensure visibility and user comfort.
- Adhere to color contrast guidelines to enhance accessibility.

### Typography
- Utilize San Francisco for readability and consistency with iOS design conventions.
- Follow recommended hierarchy (size, weight, spacing) for clarity.

### Icons and Touch Targets
- Ensure icons (e.g., audio playback) meet minimum touch target size (at least 44x44 points).
- Use standard iOS icons for familiarity and ease of use.

### Animation and Transitions
- Use subtle animations (fade, slide) for card flips or modular interactions to enhance user experience without overwhelming them.
- Ensure smooth transitions to maintain performance on all devices.

### Accessibility
- Include VoiceOver support for visually impaired users.
- Ensure text can be resized without losing functionality or design integrity.

## Recommended Design Choice: Modular Design Concept

### Justification
The Modular Design offers the best balance between user engagement and information clarity. It allows users to interactively engage with word details while offering a sleek and modern aesthetic. This design meets the following criteria:
- **User Needs**: Provides a unique learning experience by separating information into digestible parts.
- **Target Audience**: Appeals to a diverse audience, from casual learners to studying linguists.
- **iOS Platform Requirements**: Aligned with HIG principles, ensuring accessibility and user-friendly interactions.
- **Technical Feasibility**: Well within the capabilities of the Ionic React ecosystem, making it easier for architects to implement effectively.

## Conclusion
This design concept document lays the groundwork for redesigning the word card in the Dicty app. The selected modular design promotes a modern user experience, robustness, and adherence to iOS standards that ensure both usability and accessibility. Further steps will include prototyping and user testing to gather feedback before final implementation.

----------

# Architecture Document for Dicty

## Project Overview
This architecture document outlines the technical setup and principles for the Dicty app based on the approved design concept, particularly focusing on the redesign of the word card component. It aligns with the iOS Human Interface Guidelines (HIG) to ensure usability across iOS devices. 

## 1. Component Tree

### WordCard Component Structure
```plaintext
WordCard
 ├── CardFront
 │      ├── WordDisplay (Text component for the word)
 │      ├── TranscriptionDisplay (Text component for the transcription)
 │      └── AudioButton (Button for audio playback)
 └── CardBack
        ├── ExamplesList (List of examples)
        └── AdditionalDefinitions (Optional component for extra definitions)
```

### Additional Components
```plaintext
App
 ├── Header (Navigation)
 ├── WordCard (Display of the word card including interactions)
 └── Footer (Navigation or interaction controls)
```

## 2. Routing
Using **React Router** for navigation, we will define routes to separate components based on user interactions.

```javascript
import { BrowserRouter as Router, Switch, Route } from 'react-router-dom';

const App = () => (
  <Router>
    <Header />
    <Switch>
      <Route path="/word/:id" component={WordCard} />
      <Route path="/" component={Home} />
    </Switch>
    <Footer />
  </Router>
);
```

## 3. State Management
For state management, we will use **Zustand** due to its simplicity in managing global state without too much boilerplate.

### Global State Structure

```javascript
import create from 'zustand';

const useStore = create((set) => ({
  wordCardData: null,
  setWordCardData: (data) => set({ wordCardData: data }),
}));
```

## 4. Capacitor Plugins
To enhance functionality, the following Capacitor plugins will be implemented:

- **Camera**: To allow users to upload images related to the words.
- **Geolocation**: To potentially tie words to geographical locations for learning context.
- **Push Notifications**: To remind users to revisit words.
- **Biometrics**: For securing user accounts (Touch ID/Face ID).
- **Secure Storage**: To store user preferences securely.

### Plugin Configuration (iOS)

```bash
npm install @capacitor/camera @capacitor/geolocation @capacitor/push-notifications @capacitor/device @capacitor/storage
npx cap sync
```

## 5. iOS Specific Configurations

### Compliance with iOS HIG
- **Color Scheme**: Utilize dynamic color palettes based on the light and dark modes of iOS.
- **Typography**: Implementation of San Francisco for text across the app, with careful attention to sizes and spacings. 
- **Icons**: Ensure the use of SF Symbols for consistency in UI.
- **Touch Targets**: Confirm that all touchable icons and buttons have a minimum target size of 44x44 points as per iOS best practices.
- **Animations**: Keep animations subtle and responsive, using `React Spring` to manage transitions smoothly.
  
### Code for iOS Specific Enhancements
```javascript
const fadeIn = {
  from: { opacity: 0 },
  to: { opacity: 1 },
};

const ExampleList = () => {
  const { wordCardData } = useStore();
  return (
    <animated.div style={useSpring(fadeIn)}>
      {wordCardData.examples.map((example) => (
        <p key={example.id}>{example.text}</p>
      ))}
    </animated.div>
  );
};
```

## 6. Accessibility Features
- **VoiceOver**: All components will have labels for accessibility.
- **Dynamic Text Size**: Ensure that text components can adjust size dynamically based on user settings.
- **Keyboard Navigation**: Enable users to navigate the app using keyboard shortcuts where applicable.

## Conclusion
The outlined architecture for the Dicty app emphasizes modular design principles while adhering to the iOS Human Interface Guidelines. This document provides a foundation for the development team to build a user-friendly language learning application, ensuring optimal scalability, maintainability, and compliance with platform-specific requirements. Moving forward, prototyping can begin alongside integration testing to solidify the architecture effectively.

----------