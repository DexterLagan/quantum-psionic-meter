# Psionic Meter v1.0 - Technical Documentation

## System Architecture

### Overview

The Psionic Meter implements a real-time consciousness-matter interaction testing system through a carefully orchestrated pipeline of data generation, analysis, and visualization. The system operates on a continuous 60Hz cycle, maintaining precise timing for scientific validity.

### Core Components

#### 1. Random Number Generation System
```javascript
Configuration = {
    sampleRate: 60,          // Samples per second
    bufferSize: 600,         // Total samples maintained
    demoInjectionEvery: 60,  // Demo mode injection frequency
    demoInjectionDuration: 10 // Demo mode injection duration
}
```

The random number generator produces values with the following characteristics:
- Distribution: Uniform
- Range: [-1.0, 1.0]
- Frequency: 60Hz (16.67ms intervals)
- Method: Math.random() with linear transformation

#### 2. Statistical Analysis Pipeline

The system maintains a circular buffer implementation for efficient data management:

```javascript
SystemState = {
    buffer: float[600],      // Circular sample buffer
    sum: float,              // Running sum for O(1) average
    lastUpdate: timestamp,   // Last update timestamp
    isRunning: boolean,      // System operational status
    isDemoMode: boolean,     // Demo mode status
    demoCounter: integer,    // Demo mode timing counter
    isInjecting: boolean,    // Demo injection status
}
```

Key Features:
- O(1) rolling average calculation
- No memory allocation during normal operation
- Automatic buffer management
- Timestamp-based update scheduling

#### 3. Visualization System

The visualization layer utilizes Plotly.js with the following configuration:

```javascript
const layout = {
    title: false,
    showlegend: false,
    margin: { t: 40, b: 60, l: 80, r: 100 },
    paper_bgcolor: 'rgba(0,0,0,0)',
    plot_bgcolor: 'rgba(0,0,0,0)',
    xaxis: {
        range: [0, 10],
        title: {
            text: 'Seconds Ago',
            font: { color: '#4b5563', size: 14 },
            standoff: 20
        },
        fixedrange: true,
        autorange: 'reversed'
    },
    yaxis: {
        range: [-1, 1],
        title: {
            text: 'Average',
            font: { color: '#4b5563', size: 14 },
            standoff: 30
        },
        fixedrange: true,
        tickvals: Array.from({length: 21}, (_, i) => -1 + i * 0.1)
    }
}
```

## Implementation Details

### 1. Data Pipeline

The system implements a robust data pipeline with the following stages:

```
Generation → Validation → Buffer Management → Statistical Analysis → Visualization
```

Each stage operates with specific timing constraints:
- Generation: ≤1ms
- Validation: ≤0.5ms
- Buffer Management: ≤0.5ms
- Statistical Analysis: ≤1ms
- Visualization: ≤13ms

### 2. Memory Management

The system employs several strategies to maintain stable memory usage:

```javascript
// Circular Buffer Implementation
function updateBuffer(newValue) {
    if (buffer.length >= CONFIG.bufferSize) {
        sum -= buffer.shift();  // Remove oldest value
    }
    buffer.push(newValue);      // Add new value
    sum += newValue;            // Update running sum
}
```

Key Memory Features:
- Fixed-size circular buffer
- Pre-allocated visualization arrays
- Garbage collection minimization
- Efficient DOM updates

### 3. Error Handling

The system implements comprehensive error handling:

```javascript
ErrorHandling = {
    resourceLoading: RetryWithFallback,
    stateTransitions: GracefulRecovery,
    memoryExhaustion: AutomaticReset,
    visibilityChange: CleanPause
}
```

Recovery Procedures:
1. Resource Loading Failures
   - Multiple CDN endpoints
   - Automatic retry logic
   - Graceful degradation
   - User feedback

2. State Management Errors
   - Automatic state recovery
   - Buffer integrity checks
   - Timeline reconciliation
   - Safe mode fallback

### 4. Performance Optimization

#### Critical Path Optimization

```javascript
function updateSystem() {
    const now = Date.now();
    const elapsed = now - state.lastUpdate;
    const requiredUpdates = Math.min(
        Math.floor(elapsed / (1000 / config.sampleRate)),
        config.bufferSize
    );
    
    // Batch process updates
    for(let i = 0; i < requiredUpdates; i++) {
        processUpdate();
    }

    state.lastUpdate = now;
}
```

#### Animation Frame Management

```javascript
function animate() {
    if(!state.isRunning) return;
    
    updateSystem();
    updateVisualization();
    
    state.animationFrame = requestAnimationFrame(animate);
}
```

## Browser Compatibility

### Minimum Requirements

```javascript
browserSupport = {
    chrome: ">=83",
    firefox: ">=77",
    safari: ">=13.1",
    edge: ">=83"
}
```

### Feature Requirements

- ES6+ Support
- RequestAnimationFrame API
- Promises/Async-Await
- CSS Grid/Flexbox
- CSS Variables
- Backdrop Filter (optional)

## Performance Metrics

### Target Specifications

1. Loading Performance
   - First Paint: < 100ms
   - First Contentful Paint: < 200ms
   - Time to Interactive: < 500ms

2. Runtime Performance
   - Main Thread Work: < 100ms per frame
   - Memory Usage: < 50MB steady state
   - Frame Budget: 16.67ms (60fps)

3. Memory Management
   - Initial Footprint: < 20MB
   - Maximum Heap: < 50MB
   - Garbage Collection: < 1ms impact

## Security Considerations

### 1. Resource Loading

```javascript
securityHeaders = {
    crossOrigin: 'anonymous',
    referrerPolicy: 'no-referrer',
    contentSecurityPolicy: {
        'script-src': [
            'self',
            'https://cdnjs.cloudflare.com',
            'https://cdn.plot.ly'
        ]
    }
}
```

### 2. Data Handling
- Local processing only
- No network transmission
- No persistent storage
- No PII collection

## Demo Mode Implementation

### 1. Injection Pattern

```javascript
const demoConfig = {
    frequency: 60,           // Frames between injections
    duration: 10,            // Frames per injection
    minValue: 0.8,          // Minimum injection value
    maxValue: 1.0,          // Maximum injection value
    distribution: 'uniform'  // Value distribution
}
```

### 2. State Management

```javascript
function updateDemoState() {
    if (state.isDemoMode) {
        state.demoCounter++;
        if (state.isInjecting) {
            if (state.demoCounter >= config.demoInjectionDuration) {
                state.isInjecting = false;
                state.demoCounter = 0;
            }
        } else {
            if (state.demoCounter >= config.demoInjectionEvery) {
                state.isInjecting = true;
                state.demoCounter = 0;
            }
        }
    }
}
```

## Future Optimizations

### 1. Planned Enhancements
- Web Worker implementation for computation
- SharedArrayBuffer for buffer management
- WebGL acceleration for graphics
- Service Worker for offline support

### 2. Optimization Targets
- Reduce main thread work
- Minimize garbage collection
- Improve visualization performance
- Enhance mobile device support

## Development Guidelines

### 1. Code Structure
- Modular component design
- Clear state management
- Efficient update cycles
- Comprehensive error handling

### 2. Testing Requirements
- Unit tests for core functions
- Performance benchmarks
- Browser compatibility tests
- Memory leak detection

## Build and Deployment

### 1. Build Process
- Asset optimization
- Code minification
- Tree shaking
- Critical CSS inlining

### 2. Deployment Requirements
- CDN configuration
- Cache management
- Error monitoring
- Performance tracking

---

*Last Updated: February 2025*
*Version: 1.0 (Build 001)*
*Status: Production*
