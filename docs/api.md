# Psionic Meter v1.0 - API Reference

## Core System API

### Configuration Interface

#### `PsionicMeterConfig`
Core system configuration object.

```typescript
interface PsionicMeterConfig {
    sampleRate: number;        // Samples per second (default: 60)
    bufferSize: number;        // Number of samples to maintain (default: 600)
    demoInjectionEvery: number;// Frames between demo injections (default: 60)
    demoInjectionDuration: number;// Frames per injection (default: 10)
    demoValue: () => number;   // Demo value generator function
}
```

#### Default Configuration
```javascript
const defaultConfig = {
    sampleRate: 60,
    bufferSize: 600,
    demoInjectionEvery: 60,
    demoInjectionDuration: 10,
    demoValue: () => 0.8 + Math.random() * 0.2
};
```

### State Management

#### `SystemState`
System state interface.

```typescript
interface SystemState {
    isRunning: boolean;      // System operational status
    isDemoMode: boolean;     // Demo mode status
    buffer: number[];        // Circular sample buffer
    sum: number;             // Running sum for average calculation
    demoCounter: number;     // Demo mode timing counter
    isInjecting: boolean;    // Demo injection status
    lastUpdate: number;      // Last update timestamp
    animationFrame: number;  // Animation frame reference
}
```

### Core Methods

#### `initialize()`
Initializes the Psionic Meter system.

```typescript
async function initialize(): Promise<void>
```

Parameters: None
Returns: Promise resolving when initialization is complete
Throws: Error if initialization fails

Example:
```javascript
try {
    await initialize();
    console.log('System initialized successfully');
} catch (error) {
    console.error('Initialization failed:', error);
}
```

#### `resetSystem()`
Resets the system to initial state.

```typescript
function resetSystem(): void
```

Parameters: None
Returns: void
Side effects: 
- Clears buffer
- Resets sum
- Updates display
- Resets demo counters

#### `updateSystem()`
Performs system update cycle.

```typescript
function updateSystem(): void
```

Parameters: None
Returns: void
Side effects:
- Updates buffer
- Calculates new average
- Updates visualization
- Manages demo mode timing

### Visualization API

#### Graph Configuration

```typescript
interface GraphLayout {
    title: boolean;
    showlegend: boolean;
    margin: {
        t: number;
        b: number;
        l: number;
        r: number;
    };
    xaxis: AxisConfig;
    yaxis: AxisConfig;
}

interface AxisConfig {
    range: number[];
    title: {
        text: string;
        font: {
            color: string;
            size: number;
        };
        standoff: number;
    };
    fixedrange: boolean;
    gridcolor: string;
    griddash: string;
    linecolor: string;
    tickfont: {
        color: string;
        size: number;
    };
    zerolinecolor: string;
    zerolinewidth: number;
}
```

#### `initializeGraph()`
Initializes the visualization system.

```typescript
function initializeGraph(): void
```

Parameters: None
Returns: void
Side effects:
- Creates Plotly graph
- Sets up event listeners
- Initializes layout

### Event Handlers

#### `handleStart()`
Handles system start/pause.

```typescript
function handleStart(): void
```

Parameters: None
Returns: void
Side effects:
- Toggles system state
- Updates UI
- Manages animation frame

#### `handleDemoMode()`
Toggles demo mode operation.

```typescript
function handleDemoMode(): void
```

Parameters: None
Returns: void
Side effects:
- Toggles demo state
- Updates visualization
- Manages demo timing

#### `handleReset()`
Handles system reset request.

```typescript
function handleReset(): void
```

Parameters: None
Returns: void
Side effects:
- Resets system state
- Clears visualization
- Updates UI

### Data Processing

#### `generateNumber()`
Generates random or demo values.

```typescript
function generateNumber(): number
```

Parameters: None
Returns: number in range [-1, 1] or [0.8, 1.0] in demo mode
Side effects: None

#### `updateBuffer()`
Updates the circular buffer.

```typescript
function updateBuffer(value: number): void
```

Parameters:
- value: number - New value to add

Returns: void
Side effects:
- Updates buffer
- Maintains running sum
- Manages buffer size

### Error Handling

#### `handleError()`
Processes system errors.

```typescript
function handleError(error: Error): void
```

Parameters:
- error: Error - Error object to process

Returns: void
Side effects:
- Logs error
- Updates UI
- Pauses system if necessary

### Resource Management

#### `loadPlotly()`
Loads Plotly visualization library.

```typescript
async function loadPlotly(retryCount?: number): Promise<void>
```

Parameters:
- retryCount: number (optional) - Current retry attempt

Returns: Promise resolving when Plotly is loaded
Throws: Error if loading fails after retries

### Types and Interfaces

#### `ErrorHandling`
Error handling configuration.

```typescript
type ErrorHandling = {
    resourceLoading: 'RetryWithFallback';
    stateTransitions: 'GracefulRecovery';
    memoryExhaustion: 'AutomaticReset';
    visibilityChange: 'CleanPause';
}
```

#### `SecurityHeaders`
Security configuration.

```typescript
interface SecurityHeaders {
    crossOrigin: string;
    referrerPolicy: string;
    contentSecurityPolicy: {
        'script-src': string[];
    };
}
```

### Events

#### System Events
```typescript
interface SystemEvents {
    'start': void;          // System started
    'pause': void;          // System paused
    'reset': void;          // System reset
    'demo:start': void;     // Demo mode started
    'demo:stop': void;      // Demo mode stopped
    'error': Error;         // System error
    'update': number;       // New value generated
}
```

### Performance Considerations

#### Timing Constraints
- Frame budget: 16.67ms (60fps)
- Update cycle: ≤2ms
- Visualization update: ≤8ms
- State management: ≤1ms
- Buffer operations: O(1)

#### Memory Management
- Fixed buffer size
- Pre-allocated arrays
- Minimal object creation
- Efficient updates

### Browser Support

```typescript
interface BrowserSupport {
    chrome: string;  // ">=83"
    firefox: string; // ">=77"
    safari: string;  // ">=13.1"
    edge: string;    // ">=83"
}
```

### Usage Examples

#### Basic System Operation
```javascript
// Initialize system
await initialize();

// Start data collection
handleStart();

// Toggle demo mode
handleDemoMode();

// Reset system
handleReset();
```

#### Custom Configuration
```javascript
const customConfig = {
    ...defaultConfig,
    sampleRate: 30,
    bufferSize: 300
};

// Initialize with custom config
await initialize(customConfig);
```

#### Error Handling
```javascript
try {
    await initialize();
} catch (error) {
    handleError(error);
}
```

---

*Last Updated: February 2025*
*Version: 1.0*
*Status: Production*
