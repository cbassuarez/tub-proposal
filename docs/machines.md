# Machine Learning & MLOps in THE TUB

## Overview

In THE TUB, machine learning is not a black-box effect or a cloud service. It is a small, local policy layer that sits between what the system hears and how it responds. It shapes how the tub behaves as an accompanist, how it moves sound through the WaveCave, and how it regulates its own energy over the span of a week-long installation.

Rather than generating audio directly, the models continuously decide how the underlying, hand-built DSP and spatialization engine should behave. This keeps the system responsive, bounded, and legible while still allowing for complex, adaptive behavior that would be difficult to hand-code.

---

## 1. Role of Machine Learning in THE TUB

Machine learning has a set of clear, bounded responsibilities:

1. Behavior policy  
   Map: audio features + switch state + short-term internal memory → a compact behavior descriptor.  
   This descriptor controls:
   - how dense the output can become,
   - how bright or dark the overall timbre is,
   - how supportive vs. adversarial the machine feels.

2. Spatialization policy  
   Map: behavior descriptor + spatial-form switch → spatial trajectories and distributions.  
   The model decides:
   - when to favor continuous orbits vs. jump cuts,
   - how many concurrent sound “voices” should be active,
   - how broadly sound should be spread across the speakers.

3. Mode scheduler and regulator  
   On a slower time scale, a separate process watches for:
   - extended overload (too loud, too dense, for too long),
   - extended quiet or static behavior,
   - repeated or compulsive interactions with certain switches.  
   It nudges the system toward cool-down, heightened sensitivity, or more exploratory states without overriding the visible controls.

4. Recording and listening regime  
   The ML layer also controls:
   - when the system should analyze sound in fine detail,
   - when it can safely back off to coarser features to conserve compute,
   - how much recent history should remain “hot” for potential recall.

5. Optional visual hooks  
   If visual elements (e.g., indicator lights or projections) are added in future versions, they draw from the same internal state used to shape sound, so that spatialization, timbre, and any visuals remain coordinated.

In all of these roles, ML functions as an adaptive policy in a feedback loop rather than as a monolithic “AI accompanist.”

---

## 2. Design Principles

The ML system is designed around a few principles:

1. Bounded opacity  
   The models are intentionally not fully interpretable in the sense of a simple rule table, but they are:
   - small,
   - local (running on a single machine in the gallery),
   - constrained by hard-coded limits on level, density, and spectral spread.  
   The visible switches and exposed behavior descriptors ensure that the system’s agency remains situated and accountable.

2. Reliability over novelty  
   The models must:
   - run with low latency on a laptop,
   - behave predictably under stress,
   - be testable before deployment.  
   There is no live training in the WaveCave; the installation runs on a frozen, versioned bundle of models.

3. Quiet, local data practices  
   The installation:
   - does not send data to any remote servers,
   - does not perform recognition of identity, faces, or speech content,
   - does not archive raw visitor audio.  
   If data is logged, it is in the form of non-reconstructable features and model state summaries used later to refine future iterations, not to profile visitors.

4. ML as cybernetic component, not ornament  
   Machine learning is treated as one element of a cybernetic system: a way for the tub to adapt over time and regulate itself within a web of feedback. It is not an ornamental add-on or marketing term, but a specific mechanism supporting the piece’s conceptual and sonic aims.

---

## 3. Architecture

The ML system can be understood as three small, cooperating models.

### 3.1 Behavior Model

The behavior model runs at a moderate rate (for example, 5–20 updates per second) and answers the question: “Given what I’m hearing and the current switch state, how should I behave right now?”

Inputs:
- Feature vector from the audio analysis front-end, including:
  - broadband and band-limited loudness,
  - onset rate and rhythmic density,
  - spectral centroid and bandwidth,
  - low/mid/high energy balances,
  - rough indicators of noisiness vs. pitched content.
- Switch state:
  - spatial_form (tight, orbit, fragment),
  - persona (shadow, partner, adversary),
  - memory (amnesiac, short, long),
  - timbre (acoustic, hybrid, synthetic),
  - momentary jolt / clear flags.
- Short-term internal state:
  - a compact vector summarizing recent behavior and features.

Output:
- A low-dimensional behavior descriptor with components such as:
  - density,
  - brightness,
  - aggression,
  - granularity,
  - sustain,
  - stability,
  - memory_weight.

This descriptor is used to select and parameterize the DSP chains that actually generate and transform sound (delays, filters, granular engines, spectral processes, etc.).

Implementation:
- A small recurrent model (for example, a GRU or LSTM with a handful of units) or an MLP with explicit temporal context.
- Sized to run comfortably on a laptop CPU or GPU without introducing noticeable latency.

### 3.2 Spatialization Model

The spatialization model runs at a similar or slightly slower rate and decides how sound moves through the speakers.

Inputs:
- The current behavior descriptor,
- The spatial_form switch,
- Limited information about the recent history of positions (to avoid hyperactive motion).

Outputs:
- A set of active “sound objects,” each with:
  - a virtual position in a 2D or 3D layout,
  - a motion pattern (orbit, drift, jump, cluster),
  - a spread parameter (how diffuse the sound is),
  - per-object level hints.

These outputs are converted into per-speaker gains by a separate, well-understood spatialization engine (such as VBAP, Ambisonics, or a custom crossfade matrix). The learning component chooses strategies and morphs between them, rather than reinventing low-level panning.

### 3.3 Scheduler and Regulator

The scheduler operates on longer time windows (minutes rather than seconds) and represents the system’s sense of “how things have been going lately.”

Inputs:
- Long-window aggregates of features and behavior:
  - percentage of time above certain loudness thresholds,
  - distribution of densities and behaviors,
  - counts of overload interventions and jolt presses.
- Basic information about daily time (e.g., the start of the open hours vs. late evening).

Outputs:
- Slow offsets on behavior parameters:
  - favoring calmer states after heavy overload,
  - raising sensitivity after long quiet periods,
  - encouraging exploration of less-visited states.
- Decisions to temporarily ignore compulsive actions (such as rapid jolt spamming) while still responding musically.

This model can be implemented as a simple statistical mechanism, a small learned policy, or a hybrid rule-based system informed by past data.

---

## 4. Data and Training

The models are trained using a mixture of rehearsal data, synthetic testing, and offline evaluation.

### 4.1 Data Sources

1. Studio and rehearsal sessions  
   Sessions with musicians and collaborators are recorded with:
   - multichannel audio,
   - feature streams,
   - switch movements,
   - internal state traces.  
   During or after each session, I annotate segments with high-level labels such as:
   - “supportive accompaniment,”
   - “too static,”
   - “too busy,”
   - “ideal orbiting,”
   - “fatiguing over time.”

2. Synthetic scenarios  
   To stress-test the system, I generate sessions where:
   - input signals are simple but extreme (impulses, ramps, constant noise),
   - switches follow scripted trajectories that are unlikely but possible in the gallery.  
   This ensures the models see edge cases and behave safely under them.

3. Telemetry from installations  
   For actual installations, including the WaveCave run, the system can optionally log:
   - coarse feature summaries,
   - behavior descriptors,
   - regulator interventions.  
   No raw audio is stored. These logs serve as material for future refinements, not as live feedback into the running system.

### 4.2 Training Approach

- Behavior model  
  Trained as a supervised or self-supervised mapping from feature/switch histories to behavior descriptors that produced musically successful behavior in rehearsal. Ground truth is defined by:
  - curated labels,
  - heuristics based on thresholds and diversity,
  - listening-based selection of “good” segments.

- Spatialization model  
  Trained on a curated set of spatial patterns:
  - I design target behaviors for specific descriptors (e.g., calm orbits, tense fragmentations).
  - The model learns to interpolate between these design points as the descriptor moves through its space.

- Scheduler  
  Can be primarily rule-based, with ML providing thresholds and heuristics. For example:
  - if a certain density band has been dominant for too long, the scheduler gradually biases the behavior model away from that region.

Importantly, all training happens offline, outside the gallery. The WaveCave installation uses frozen models that have been listened to and validated ahead of time.

---

## 5. MLOps and Public-Facing Repository

To keep the ML layer reproducible, inspectable, and aligned with the rest of my research practice, the entire pipeline lives in a public-facing repository.

### 5.1 Repository Contents

The repository includes:

- Model definitions for the behavior, spatialization, and scheduler components.
- Training and evaluation scripts for each model.
- Configuration files describing:
  - feature extraction parameters,
  - model hyperparameters,
  - dataset partitions.
- Data schemas describing the structure of logs and training data (without shipping raw audio).
- Documentation that explains, in plain language, how each model relates to the installation.

This public-facing repo allows faculty, technologists, and other artists to see exactly how the system’s decisions are made and how they are constrained.

### 5.2 Versioning and Bundles

Each model is versioned using semantic tags, for example:

- THE-TUB_behavior_v1.0
- THE-TUB_spatial_v1.0
- THE-TUB_scheduler_v1.0

For each installation, I create a bundle such as:

- bundle_wavecave_2026.1

which specifies the exact versions of all models, feature configurations, and safety limits. The WaveCave run uses a single, pinned bundle for its entire duration.

### 5.3 Deployment

At deployment time:

- Models are exported into compact formats (for example, ONNX, TorchScript, or simple JSON weight tables).
- The real-time audio patch loads the bundle at startup and exposes:
  - basic diagnostics (e.g., which bundle is running),
  - safe fallback behavior if model loading fails.

There is no dynamic model downloading, and no network dependence. If the ML components fail, the system falls back to a static, rule-based behavior that remains musically coherent and safe, preserving the installation.

### 5.4 Monitoring

During the run, lightweight monitoring logs:

- which behavior regions are most active,
- how often the scheduler intervenes to reduce density or level,
- how the physical switches are used over time.

These logs are periodically copied out of the gallery and committed to the public repository as anonymized summaries, forming a longitudinal record of THE TUB’s evolving practice across venues.

---

## 6. Relation to Cybernetics and Research

In the context of my dissertation and broader research, the machine-learning components of THE TUB are ways of encoding a particular set of feedback relationships in software and exposing them to public interaction.

- The behavior model embodies a learned mapping from shared sound conditions and visible controls to musical responses that have been negotiated in rehearsal.
- The spatialization model enacts a learned way of producing and distributing space in the WaveCave, taking seriously the idea that space is not neutral but produced in time by sound, bodies, and architecture.
- The scheduler formalizes the system’s ability to refuse and withdraw: to recognize when participation has become overwhelming or trivial and to nudge the installation back into sustainable, legible modes.

By publishing the models and their training pipeline in a public-facing repository, the project treats the ML layer not as personal magic but as a shared, inspectable artifact. This aligns with the broader argument of my research: that cybernetic art should make its feedback structures available for critical and creative engagement, even when those structures include contemporary machine-learning techniques.
