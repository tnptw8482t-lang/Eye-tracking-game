# Eye-Tracking Shooting Game - Game Design Specification

## Project Overview
An accessible, web-based eye-tracking shooting game designed specifically for visually impaired students to support eye control training and improve eye-gaze accuracy through interactive gameplay.

**Technology Stack:**
- HTML5
- JavaScript (ES6+)
- CSS3
- Google MediaPipe (Eye Tracking)
- Web APIs (getUserMedia, Canvas API)

---

## 1. Core Game Mechanics

### 1.1 Eye-Gaze Detection System
- **Input Method**: Real-time eye-gaze tracking via webcam using Google MediaPipe
- **Detection Accuracy**: Track iris position with estimated ±2-5cm accuracy at typical viewing distances
- **Frame Rate**: 30 FPS minimum, target 60 FPS
- **Latency**: <100ms between eye movement and game response

### 1.2 Primary Gameplay Loop
1. **Targeting**: Eye gaze controls a crosshair/cursor on screen
2. **Shooting**: Automatic trigger on gaze dwell time (adjustable, default 0.5-1 second)
3. **Hit Detection**: Collision detection between gaze point and interactive targets
4. **Scoring**: Points awarded for successful hits, feedback provided
5. **Difficulty Progression**: Targets increase in speed/size/spawn rate

### 1.3 Accessibility Features for Visually Impaired Users
- **Audio Feedback**: 
  - Different sound effects for target hits vs misses
  - Haptic feedback (if device supports)
  - Voice announcements for score, level, and game events
  - High-contrast visual indicators (though primary feedback is audio)
- **Calibration System**: 
  - 9-point calibration routine at game start
  - Real-time recalibration option during gameplay
  - Guidance prompts with audio cues
- **Gaze Stability Analysis**:
  - Monitor jitter/stability of eye position
  - Provide feedback on tracking quality
  - Auto-adjust trigger sensitivity based on stability

---

## 2. Webcam Integration & Configuration

### 2.1 Live Webcam View Display
- **Rendering**: 
  - Full-screen or windowed canvas display
  - Display webcam feed in real-time via `HTMLVideoElement`
  - Resolution: 640x480 minimum, 1280x720 recommended
  - Frame rate: Match browser's requestAnimationFrame (typically 60 FPS)
- **Display Options**:
  - Mirror/flip toggle (for self-view comfort)
  - Brightness/contrast adjustment
  - Option to show/hide webcam feed during gameplay
  - Picture-in-picture mode for webcam feed

### 2.2 Webcam Selection & Configuration
- **Device Selection**:
  - Enumerate all available cameras using `enumerateDevices()`
  - Dropdown/list interface for camera selection
  - Display selected camera name and resolution
  - Ability to switch cameras mid-game without restart
- **Constraint Configuration**:
  ```javascript
  {
    video: {
      width: { ideal: 1280 },
      height: { ideal: 720 },
      facingMode: 'user', // or 'environment'
      aspectRatio: { ideal: 16/9 }
    },
    audio: false
  }
  ```
- **Permission Handling**:
  - Request camera permission with clear explanation
  - Handle permission denial gracefully
  - Retry mechanism with troubleshooting guidance
  - Display fallback message if no camera available

### 2.3 View Correction (Mirroring/Flipping)
- **Problems to Solve**:
  - Operating system level mirroring (common on some systems)
  - Front-facing camera automatic flip
  - User preference for comfort during use
- **Solution Implementation**:
  - **Horizontal Flip**: CSS `scaleX(-1)` or canvas `scale(-1, 1)` transformation
  - **Vertical Flip**: CSS `scaleY(-1)` or canvas `scale(1, -1)` transformation
  - **Settings Menu**:
    - Checkbox: "Flip Horizontal"
    - Checkbox: "Flip Vertical"
    - "Reset to Default" button
    - Live preview of changes
  - **Persistence**: Save user preferences to localStorage
  - **Auto-Detection**: 
    - Attempt to detect if camera is already flipped
    - Provide one-click auto-correct option
    - Store correction preference per camera device

### 2.4 Camera Stream Quality Management
- **Adaptive Quality**:
  - Detect browser performance
  - Adjust resolution if frame rate drops below 30 FPS
  - Fallback chain: 1280x720 → 640x480 → 320x240
- **Error Handling**:
  - Camera disconnected mid-game: Pause and request reconnection
  - Browser permission revoked: Graceful shutdown with instructions
  - Insufficient browser support: Display compatibility message
  - Display helpful error messages with troubleshooting steps

---

## 3. Game Modes & Difficulty Levels

### 3.1 Training Mode
- **Purpose**: Eye control training for beginners
- **Features**:
  - Slower target movement (1-3 cm/s)
  - Larger targets (2-4 cm diameter)
  - Longer dwell time for trigger (1-2 seconds)
  - Clear visual/audio guidance
  - Unlimited time
  - Detailed performance analytics
- **Targets**: Simple geometric shapes (circles, squares)

### 3.2 Practice Mode
- **Purpose**: Intermediate skill development
- **Features**:
  - Medium target movement (3-8 cm/s)
  - Medium target sizes (1-2 cm diameter)
  - Standard dwell time (0.5-1 second)
  - Time limit per round: 60-120 seconds
  - Multiple target types
  - Lives system: 3 strikes before game over

### 3.3 Challenge Mode
- **Purpose**: Competitive high-score gameplay
- **Features**:
  - Fast target movement (8-15 cm/s)
  - Small targets (0.5-1 cm diameter)
  - Quick trigger (0.3-0.5 second dwell time)
  - Time limit: 180 seconds
  - Dynamic difficulty (speeds up every 30 seconds)
  - Leaderboard support
  - Power-ups and modifiers

### 3.4 Calibration Mode
- **Purpose**: System calibration and fine-tuning
- **Procedure**:
  - 9-point grid calibration (like eye-tracker setup)
  - User follows dot with eyes while system records gaze positions
  - Audio cues guide user through calibration sequence
  - Real-time accuracy feedback
  - Calculate calibration confidence score
  - Option to recalibrate specific areas

---

## 4. User Interface & Controls

### 4.1 Main Menu Screen
- **Buttons/Options**:
  - Start Game (leads to mode selection)
  - Settings
  - Calibration
  - Help/Tutorial
  - High Scores
  - About
- **Keyboard Shortcuts** (for accessibility):
  - Tab: Navigate menu items
  - Enter: Activate button
  - Esc: Back to menu

### 4.2 Settings Screen
```
┌─────────────────────────────────────┐
│          GAME SETTINGS              │
├─────────────────────────────────────┤
│ Webcam & Camera                     │
│  ├─ Select Camera: [Dropdown] ▼    │
│  ├─ Camera Resolution: 1280x720    │
│  ├─ ☑ Flip Horizontal              │
│  ├─ ☐ Flip Vertical                │
│  └─ [Auto-Detect Flip]             │
│                                     │
│ Gameplay Settings                   │
│  ├─ Dwell Time: [────●───] 0.7s    │
│  ├─ Target Size: [──●─────] Medium │
│  ├─ Sensitivity: [───●────] High   │
│  └─ ☑ Audio Feedback               │
│                                     │
│ Visual Settings                     │
│  ├─ Brightness: [──●─────] +0.2    │
│  ├─ Contrast: [──●─────] +0.1      │
│  ├─ ☑ Show Crosshair               │
│  ├─ ☑ Show Gaze Trail              │
│  └─ Crosshair Style: [Circle▼]     │
│                                     │
│ Accessibility                       │
│  ├─ ☑ Voice Announcements          │
│  ├─ Speech Speed: [───●────] 1.0x  │
│  ├─ ☑ High Contrast Mode           │
│  └─ Font Size: [──●─────] Large    │
│                                     │
│ [Save Settings]  [Reset to Default]│
└─────────────────────────────────────┘
```

### 4.3 Settings Details

#### Camera Settings
- Camera selection dropdown with device names
- Display current resolution and FPS
- Horizontal/Vertical flip toggles
- Auto-detect flip correction
- Test camera button (shows live feed)

#### Gameplay Configuration
- **Dwell Time Slider** (0.3s - 2.0s)
  - Time eye must stay on target before shooting
  - Default: 0.7s
  - Lower = faster gameplay, higher difficulty
  - Stored per user profile
- **Target Size Control** (Small, Medium, Large)
  - Easy: 4-5 cm diameter
  - Medium: 2-3 cm diameter
  - Hard: 0.5-1 cm diameter
- **Gaze Sensitivity** (Low, Medium, High)
  - Controls deadzone and smoothing
  - Lower = more forgiving, easier to target
  - Higher = more responsive but twitchier
- **Trigger Sound Volume** (0-100%)
- **Background Music Volume** (0-100%)

#### Visual Settings
- Brightness/Contrast adjustment (±50%)
- Crosshair visibility toggle
- Crosshair color options
- Gaze point trail display (shows last N points)
- Screen blinking reduction mode

#### Accessibility Enhancements
- Text-to-speech voice announcements
- Speech speed adjustment (0.5x - 2.0x)
- High contrast color scheme toggle
- Font size adjustment
- Keyboard-only mode support
- Screen reader compatibility

### 4.4 Controls During Gameplay
- **Primary Control**: Eye gaze position
- **Secondary Controls** (keyboard fallback):
  - **P**: Pause/Resume
  - **R**: Reset/Restart
  - **S**: Settings
  - **Esc**: Quit to menu
  - **Space**: Manual shoot (for testing)
- **Haptic Feedback** (if available):
  - Vibration on successful hit
  - Haptic pulse on target spawn
  - Different patterns for different events

---

## 5. Target System

### 5.1 Target Types
1. **Standard Circles**
   - Basic target, 1 point
   - Color: Bright cyan/green for visibility
   - Audio: Single beep on hit

2. **Bonus Targets**
   - Larger, slower moving
   - Color: Gold/yellow
   - Points: 5 points
   - Audio: Ascending tones

3. **Rapid Targets**
   - Small, fast-moving
   - Color: Red
   - Points: 10 points
   - Audio: High-pitched beep

4. **Challenge Targets**
   - Move in complex patterns
   - Color: Purple
   - Points: 20 points
   - Audio: Complex sound pattern

5. **Hazard Targets** (optional)
   - Lose points if hit
   - Color: Dark red/black
   - Points: -5
   - Audio: Error tone

### 5.2 Target Behavior
- **Spawn Logic**:
  - Random positions with minimum spacing (avoid overlap)
  - Spawn every 2-5 seconds (mode dependent)
  - Maximum 5-8 targets visible at once
  - Diagonal movement patterns
  
- **Movement Patterns**:
  - Linear: Straight line across screen
  - Circular: Orbital motion
  - Sinusoidal: Wave pattern
  - Random: Erratic movement
  
- **Collision**:
  - Hit zone: ±2cm radius of target center
  - Immediate score feedback
  - Target disappears and respawns
  - Combo counter increases on consecutive hits

### 5.3 Visual & Audio Feedback
- **On Hit**:
  - Visual: Explosion/burst animation, color flash
  - Audio: Satisfying "ping" or "ding" sound
  - Haptic: Vibration pulse
  - UI: Score popup floating upward
  
- **On Miss**:
  - Audio: Soft error tone
  - UI: Brief crosshair color change
  - No score deduction (except hazards)
  
- **Combo Feedback**:
  - Audio: Musical notes ascending for x3, x5, x10 combos
  - UI: Combo counter display with increasing size
  - Visual: Color transition (cyan → green → gold → red)

---

## 6. Scoring System

### 6.1 Points Calculation
```
Base Points = (Target Type Points) × (Difficulty Multiplier) × (Accuracy Bonus)

Where:
- Target Type Points: 1-20 depending on target
- Difficulty Multiplier: 1.0 (Easy) → 1.5 (Medium) → 2.0 (Hard)
- Accuracy Bonus: 1.0-1.5 based on proximity to target center
```

### 6.2 Combo System
- Consecutive hits: ×1.1, ×1.2, ×1.3... multiplier
- Reset on miss (no penalty)
- Audio/visual celebration at ×5, ×10, ×20

### 6.3 Performance Metrics
- **Accuracy**: Hits ÷ Total shots fired (%)
- **Hit Rate**: Hits per minute
- **Average Hit Time**: Average dwell time before trigger
- **Gaze Stability**: Smoothness of eye tracking
- **Reaction Time**: Time from target spawn to hit

### 6.4 High Score Tracking
- Store top 10 scores locally (localStorage)
- Include: Name, Score, Date, Mode, Difficulty
- Export/Import functionality
- Per-difficulty leaderboards

---

## 7. Audio Design

### 7.1 Sound Effects
| Event | Sound | Duration | Volume |
|-------|-------|----------|--------|
| Target Hit | "ping" tone (800Hz) | 150ms | Medium |
| Combo Hit | Ascending notes | 300ms | Medium |
| Level Up | Success chime | 500ms | Medium |
| Error/Miss | Error buzz (low) | 200ms | Soft |
| Calibration Point | Beep | 100ms | Soft |
| Game Over | Defeat fanfare | 800ms | Medium |
| Menu Select | Click sound | 100ms | Soft |

### 7.2 Voice Announcements
- **Game Start**: "Game started. Mode: Training. Ready."
- **Level Up**: "Level 2 achieved. Targets moving faster."
- **Combo**: "Combo 5x. Great accuracy!"
- **Score**: "Score 250 points."
- **Game Over**: "Game over. Final score: 1250 points."
- **Calibration**: "Look at the dot in the upper left corner."

### 7.3 Background Music
- Adaptive background music (changes with difficulty)
- Toggle on/off in settings
- Lower volume during voice announcements
- No music during calibration

---

## 8. Calibration System

### 8.1 Initial Calibration
**Procedure**:
1. Display 9-point grid on screen
2. Audio prompt: "Calibrating eye tracker. Look at each dot."
3. Guide user through 9 positions (grid pattern):
   - Top-left → Top-center → Top-right
   - Middle-left → Center → Middle-right
   - Bottom-left → Bottom-center → Bottom-right
4. At each point:
   - Audio cue: "Look at dot"
   - Record gaze position for 1 second
   - Audio confirmation: "Got it"
5. Calculate offset mapping from detected gaze to screen position

### 8.2 Calibration Accuracy
- **Quality Indicator**: 
  - Show confidence score (0-100%)
  - Green: 90%+ (Excellent)
  - Yellow: 70-89% (Good)
  - Red: <70% (Try again)
- **Precision Test**:
  - After calibration, user looks at 5 random points
  - System shows accuracy estimate
  - Option to recalibrate if needed

### 8.3 Recalibration Options
- **Full Recalibration**: 9-point grid (1-2 min)
- **Quick Recalibration**: 3-point validation (30 sec)
- **Spot Correction**: Recalibrate specific area if accuracy is off
- **Auto Recalibration**: Detect accuracy drift and prompt user

### 8.4 Calibration Data Management
- Save calibration profile locally
- Multiple user profiles support
- Apply calibration per camera device
- Reset calibration on demand

---

## 9. Technical Architecture

### 9.1 File Structure
```
eye-tracking-game/
├── index.html
├── css/
│   ├── styles.css          (Main stylesheet)
│   ├── menu.css            (Menu styles)
│   ├── game.css            (Game UI styles)
│   └── responsive.css      (Mobile/responsive)
├── js/
│   ├── main.js             (Entry point)
│   ├── game.js             (Game loop, core logic)
│   ├── mediapipe-handler.js (Eye tracking integration)
│   ├── camera.js           (Webcam management)
│   ├── ui-manager.js       (Menu & settings UI)
│   ├── calibration.js      (Calibration system)
│   ├── audio-manager.js    (Sound effects & speech)
│   ├── score-manager.js    (Scoring & leaderboards)
│   ├── settings.js         (Settings persistence)
│   └── utils.js            (Helper functions)
├── assets/
│   ├── sounds/             (Audio files)
│   ├── fonts/              (Custom fonts)
│   └── images/             (Icons, splash screens)
├── lib/
│   └── mediapipe/          (MediaPipe library)
└── README.md
```

### 9.2 Key JavaScript Classes/Objects

**GameEngine**
```javascript
class GameEngine {
  constructor(config)
  start()
  pause()
  resume()
  stop()
  update(gaze_position)
  render()
  calculateScore()
}
```

**EyeTracker**
```javascript
class EyeTracker {
  constructor(video_element)
  initialize()
  startTracking()
  stopTracking()
  setCalibration(points)
  getGazePosition()
  getConfidence()
  recalibrate()
}
```

**CameraManager**
```javascript
class CameraManager {
  listDevices()
  selectDevice(device_id)
  startStream(constraints)
  stopStream()
  flipHorizontal(enabled)
  flipVertical(enabled)
  adjustBrightness(value)
  adjustContrast(value)
}
```

**UIManager**
```javascript
class UIManager {
  showMainMenu()
  showSettings()
  showGame()
  updateScore(score)
  updateCombo(combo)
  showCalibration()
  updateCrosshair(x, y)
}
```

### 9.3 Data Flow
```
Webcam Stream → MediaPipe → Gaze Coordinates
                              ↓
                        Calibration Mapping
                              ↓
                        Screen Coordinates (x,y)
                              ↓
                        Collision Detection
                              ↓
                        Score Calculation
                              ↓
                        UI Update & Audio Feedback
```

### 9.4 Browser APIs Required
- `getUserMedia()` - Webcam access
- `enumerateDevices()` - Camera device listing
- `Canvas API` - Rendering
- `Web Audio API` - Sound effects
- `Web Speech API` - Voice announcements (optional)
- `localStorage` - Settings & score persistence
- `requestAnimationFrame()` - Animation loop

### 9.5 Third-Party Libraries
- **MediaPipe**: `@mediapipe/tasks-vision`
- **Optional**: Tone.js (for audio generation)
- **Optional**: Three.js (for advanced 3D targets)

---

## 10. Performance Optimization

### 10.1 Frame Rate Management
- Target: 60 FPS (minimum 30 FPS)
- Monitor frame time and adjust quality:
  - Reduce target count if FPS < 30
  - Lower resolution if needed
  - Disable gaze trail if needed
  
### 10.2 Memory Management
- Reuse canvas/objects instead of creating new ones
- Implement object pooling for targets
- Clean up event listeners on game exit
- Limit gaze trail to last 30 points

### 10.3 Network & Asset Loading
- Lazy load MediaPipe model on first use
- Cache MediaPipe WASM files
- Minimize asset sizes
- Consider CDN for libraries

---

## 11. Accessibility & Inclusive Design

### 11.1 For Visually Impaired Users
- **Primary Feedback**: Audio
  - Distinct sound signatures for events
  - Voice announcements for all important info
  - Screen reader compatible UI
  
- **Secondary Feedback**: Haptic
  - Vibration on hit/miss
  - Different patterns for different events
  
- **Tertiary Feedback**: Tactile
  - Physical button interactions (keyboard)
  - Clear button positions and labels

### 11.2 Keyboard Navigation
- Full keyboard support
- Logical tab order through menus
- Enter/Space to activate buttons
- Arrow keys to adjust sliders
- Mnemonic shortcuts (Alt+key)

### 11.3 Screen Reader Support
- Semantic HTML (button, label, etc.)
- ARIA labels and descriptions
- Live regions for dynamic content updates
- Skip navigation links

### 11.4 Color & Contrast
- High contrast color palette
- WCAG AA compliant colors
- Not relying on color alone for information
- Alternative visual indicators

### 11.5 Motor Accessibility
- Large click targets (44x44px minimum)
- Adjustable dwell time
- Calibration assistance
- No time pressure requirement

---

## 12. Testing & Quality Assurance

### 12.1 Test Cases
| Category | Test | Expected Result |
|----------|------|-----------------|
| Webcam | Camera initializes | Stream displays |
| Webcam | Multiple cameras | Can switch between them |
| Flip | Horizontal flip toggles | Image mirrors correctly |
| Flip | Vertical flip toggles | Image flips vertically |
| Calibration | 9-point calibration | Accuracy within ±2cm |
| Game | Target spawns | Appears on screen after delay |
| Game | Hit detection | Score increases on hit |
| Game | Combo system | Multiplier increases correctly |
| Audio | Sound effects | Play at correct times |
| Audio | Voice | Announcements play clearly |
| Settings | Save settings | Persist after reload |
| Performance | Frame rate | Maintains 30+ FPS |

### 12.2 Browser Compatibility
- Chrome/Chromium (v90+)
- Firefox (v88+)
- Safari (v15+)
- Edge (v90+)
- Mobile browsers (where applicable)

### 12.3 Accessibility Testing
- WAVE accessibility checker
- Axe DevTools
- Screen reader testing (NVDA, JAWS)
- Keyboard-only navigation
- Color contrast verification

---

## 13. Future Enhancements

### 13.1 Phase 2 Features
- Multiplayer competitive mode
- Custom target creation
- Advanced analytics & progress tracking
- Machine learning-based difficulty adaptation
- Integration with VR headsets

### 13.2 Phase 3 Features
- AR mode (browser-based AR)
- Cloud save synchronization
- Social features (challenges, sharing)
- Educational progress reports
- Integration with educational platforms (LMS)

### 13.3 Phase 4 Features
- Mobile app version (React Native)
- Offline mode
- Advanced eye-gaze gestures
- Gaze-controlled video playback
- Integration with accessibility software

---

## 14. Deployment & Distribution

### 14.1 Hosting Options
- GitHub Pages (free, static)
- Netlify (free tier available)
- Vercel (free tier)
- Self-hosted on school/institutional server

### 14.2 Installation
- Access via web browser (no installation)
- No software dependencies required
- Can be used online or downloaded for offline use

### 14.3 Documentation
- User manual with screenshots
- Quick start guide
- Troubleshooting guide
- Developer documentation
- API reference

---

## 15. Success Metrics & Evaluation

### 15.1 User Engagement
- Average session duration
- Return user rate
- High scores achieved
- Difficulty level progression

### 15.2 Skill Development (for visually impaired students)
- Improvement in gaze accuracy over time
- Reduction in dwell time needed
- Increase in target hit rate
- Consistency of eye movement

### 15.3 Technical Performance
- Frame rate consistency
- Calibration accuracy
- System stability (crash-free sessions)
- Loading time (<3 seconds)

### 15.4 Accessibility Compliance
- WCAG 2.1 AA standard compliance
- Screen reader functionality
- Keyboard navigation completeness
- Audio feedback adequacy

---

## Appendix A: Color Palette

```
Background: #000000 (Pure Black - for contrast)
Primary UI: #00FFFF (Cyan - high visibility)
Secondary UI: #00FF00 (Bright Green - accessible)
Accent: #FFFF00 (Yellow - warning/bonus)
Danger: #FF0000 (Red - hazards)
Success: #00FF00 (Green - correct)
Error: #FF6600 (Orange - errors)
Text: #FFFFFF (White)

High Contrast Mode:
Background: #000000
Text: #FFFFFF
Active: #FFFF00
Disabled: #808080
```

---

## Appendix B: Audio Specifications

```
Sound Effects:
- Format: MP3 or OGG (browser compatible)
- Sample Rate: 44.1 kHz
- Bit Depth: 16-bit
- Target Level: -3dB (normalized)

Voice:
- Use Web Speech API Text-to-Speech
- Speaking Rate: 0.8x - 1.5x (adjustable)
- Language: Multiple options supported
- Fallback: Audio files if TTS not available
```

---

## Appendix C: Calibration Grid Positions

```
Screen coordinates for 9-point calibration:
[10%, 10%]   [50%, 10%]   [90%, 10%]
[10%, 50%]   [50%, 50%]   [90%, 50%]
[10%, 90%]   [50%, 90%]   [90%, 90%]

Each point displays for 1-2 seconds
User should look directly at each point
System records multiple gaze samples at each position
```

---

**Document Version**: 1.0  
**Last Updated**: 2026-06-18  
**Status**: Ready for Implementation  
**Target Audience**: Developers, Educators, Students with Visual Impairments
