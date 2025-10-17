# ReRAM-Based-Ultra-Low-Power-ECG-Anomaly-Detector-with-Patient-Adaptive-Learning

## Executive Summary
We propose a groundbreaking ECG anomaly detection SoC that leverages BM Labs' ReRAM NVM IP to achieve sub-10μW continuous cardiac monitoring with patient-specific adaptation. Our design addresses a critical healthcare need: detecting cardiac arrhythmias hours before symptoms appear, potentially saving millions of lives annually.

## Key Innovation:
First ECG processor to use ReRAM for storing personalized cardiac signatures that persist without power, enabling truly adaptive, patient-specific anomaly detection with 100x lower power than existing solutions.

## Problem Statement
5 million Americans have Atrial Fibrillation (AFib), causing 150,000+ deaths annually 75% of AFib episodes are asymptomatic and go undetected
Current continuous ECG monitors last only 24-48 hours on battery
Existing solutions lack personalization, causing 65% false positive rates
Critical need for ultra-low power, accurate, continuous monitoring

Our Solution: ReRAM-Enabled Cardiac Intelligence
Revolutionary Features

Persistent Patient Memory: ReRAM stores each patient's unique cardiac signature
10μW Average Power: Enables 6-month operation on coin cell battery
On-Chip Learning: Adapts to patient's changing cardiac patterns over time
Multi-Condition Detection: AFib, PVC, bradycardia, tachycardia, and ischemia
Real-Time Response: Detection latency <100ms for critical events

Technical Architecture
System Overview
```
┌─────────────────────────────────────────────────────────────────┐
│                    ECG Anomaly Detection SoC                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌─────────────┐    ┌──────────────┐    ┌─────────────────┐   │
│  │   Analog    │───►│   DSP Core   │───►│ Feature Extract │   │
│  │  Front-End  │    │  - Filters   │    │  - R-peaks      │   │
│  │  12-bit ADC │    │  - Denoising │    │  - RR intervals │   │
│  │   250 Hz    │    │  - Baseline  │    │  - QRS width    │   │
│  └─────────────┘    └──────────────┘    └────────┬────────┘   │
│                                                    ▼            │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │              ReRAM Neuromorphic Processor               │   │
│  ├─────────────────────────────────────────────────────────┤   │
│  │  ┌─────────────┐  ┌─────────────┐  ┌────────────────┐  │   │
│  │  │  NVM IP     │  │  Template   │  │   Anomaly      │  │   │
│  │  │  32x32      │  │  Matching   │  │   Classifier   │  │   │
│  │  │  Crossbar   │  │   Engine    │  │   (Neural Net) │  │   │
│  │  └─────────────┘  └─────────────┘  └────────────────┘  │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                 Alert & Power Management                 │   │
│  │  - Critical event detection → Immediate alert            │   │
│  │  - Adaptive duty cycling based on cardiac stability      │   │
│  │  - Patient profile switching for multi-patient use       │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  Wishbone │ SPI │ I2C │ GPIO │ Clock Gen │ Power Domains      │
└─────────────────────────────────────────────────────────────────┘
```

### ReRAM Memory Organization
```
ReRAM 32x32 Crossbar Allocation:
┌─────────────────────────────────┐
│ Rows 0-7:   Patient Templates   │  // 8 personalized normal ECG patterns
│ Rows 8-15:  Anomaly Signatures  │  // Known arrhythmia patterns
│ Rows 16-23: Neural Weights      │  // Classifier network (8x16 neurons)
│ Rows 24-27: Adaptive Parameters │  // Thresholds, scaling factors
│ Rows 28-31: Patient History     │  // Recent events, trends
└─────────────────────────────────┘
```

### Power Architecture
```
Power Modes:
- SLEEP:    0.5 μW  (Only RTC active, waiting for next sample)
- SAMPLE:   5 μW    (ADC + minimal digital, 250Hz)
- ANALYZE:  50 μW   (Full processing, triggered on detected beats)
- ALERT:    200 μW  (Anomaly detected, transmitting alert)
- ADAPT:    500 μW  (Updating ReRAM weights, rare event)

Duty Cycle (Normal Rhythm):
- Sleep: 95% | Sample: 4% | Analyze: 1% → Average: 10 μW
