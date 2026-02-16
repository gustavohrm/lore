# Project Roadmap

## AI Roleplay Chat Application

**Last Updated:** February 16, 2026  
**Status:** Planning

> **For AI Assistants**: This roadmap defines development phases, feature prioritization, and milestone objectives. Use this to understand the current development stage and upcoming work. Reference `PRD.md` for detailed feature specifications.

---

## Development Philosophy

**Approach**: Build incrementally, validate continuously, optimize later.

**Priorities**:

1. **Functionality first**: Get core features working locally
2. **Test critical paths**: Database and AI provider integrations
3. **Iterate on prompts**: AI behavior will require tuning
4. **Optimize when stable**: Performance improvements come after MVP

**Not priorities for MVP**:

- CI/CD pipelines
- Advanced performance optimization
- Enterprise-scale features
- Deployment infrastructure beyond basic Cloudflare Workers

---

## Phase 0: Foundation (Week 1-2)

### Objectives

Establish core infrastructure and development environment.

### Tasks

#### 0.1 Project Setup

- [x] Initialize Vite project
- [x] Configure TypeScript
- [x] Set up TailwindCSS
- [x] Configure Vitest
- [x] Create folder structure
- [ ] Set up basic CSS theme variables
- [ ] Create `wrangler.toml` for Cloudflare Workers

#### 0.2 Core Utilities

- [ ] Implement custom markdown parser (`mdParser.ts`)
  - Support bold, italic, code blocks
  - Support action syntax (e.g., `*walks away*`)
  - Return parsed HTML
- [ ] Create alert/notification system (`alert.ts`)
  - Toast notifications
  - Error messages
  - Success confirmations
- [ ] Set up custom event system for cross-module communication

#### 0.3 Database Layer

- [ ] Design IndexedDB schema
  - Chats table
  - Messages table
  - Events table
  - Media table (images/audio)
  - Preferences table
  - API keys table
- [ ] Implement `BaseRepository<T>` class
  - CRUD operations
  - Transaction support
  - Error handling
- [ ] Create entity-specific repositories
  - `ChatRepository`
  - `MessageRepository`
  - `EventRepository`
  - `MediaRepository`
  - `PreferencesRepository`
  - `ApiKeyRepository`
- [ ] Write unit tests for all repository operations
- [ ] Test database migrations/schema updates

**Milestone**: Database fully functional with >80% test coverage

---

## Phase 1: AI Provider Integration (Week 3-4)

### Objectives

Implement provider contracts and integrate text generation.

### Tasks

#### 1.1 Provider Contracts

- [ ] Define `TextGenerationProvider` interface
- [ ] Define `ImageGenerationProvider` interface
- [ ] Define `AudioGenerationProvider` interface
- [ ] Create shared types (`GenerationParams`, `GenerationResult`, etc.)
- [ ] Document contract requirements

#### 1.2 Text Generation Providers

- [ ] Implement OpenAI provider (`openai.ts`)
  - API client setup
  - Response streaming support
  - Error handling
  - Model listing
  - API key validation
- [ ] Implement Google Cloud provider (`google-cloud.ts`)
  - Same features as OpenAI
- [ ] Implement OpenRouter provider (`open-router.ts`)
  - Same features as OpenAI
- [ ] Write unit tests for each provider
- [ ] Test provider switching mid-conversation

#### 1.3 Structured Output

- [ ] Design `AIResponse` interface
  - Message content
  - Emotion updates (nullable)
  - Tool calls for memory retrieval
  - Suggested actions (image generation)
  - Event descriptions
- [ ] Implement structured output parsing
- [ ] Handle malformed responses gracefully
- [ ] Test with all providers

#### 1.4 Initial System Prompt

- [ ] Draft base system prompt template
  - Character definition injection
  - Emotion tracking instructions
  - Tool calling guidelines
  - Event generation rules
- [ ] Test prompt with different scenarios
- [ ] Iterate based on response quality

**Milestone**: Text generation working with all providers, structured output reliable

---

## Phase 2: Chat Core (Week 5-6)

### Objectives

Build basic chat functionality without advanced features.

### Tasks

#### 2.1 Chat Module

- [ ] Implement chat creation flow
  - New chat modal UI
  - Plot/character input forms
  - Validation
  - Save to database
- [ ] Implement message exchange
  - User message input
  - AI response generation
  - Message display in UI
  - Markdown rendering
- [ ] Implement chat list view
  - Display all chats
  - Sort by last message
  - Search/filter functionality
  - Delete chat functionality
- [ ] Implement chat persistence
  - Auto-save on every message
  - Load chat history on resume
  - Handle large chat histories

#### 2.2 Basic UI Components

- [ ] Message bubble component (user/AI variants)
- [ ] Chat input component
- [ ] Chat list item component
- [ ] Modal/dialog component
- [ ] Button components
- [ ] Loading/skeleton states

#### 2.3 Context Management (Simple Version)

- [ ] Implement basic context composition
  - System prompt + plot + last 10 messages
  - No memory retrieval yet
  - No event table yet
- [ ] Test context window limits
- [ ] Ensure conversation continuity

**Milestone**: Users can create chats, exchange messages, see history

---

## Phase 3: Emotion Tracking (Week 7)

### Objectives

Implement AI emotion state tracking and display.

### Tasks

#### 3.1 Emotion System

- [ ] Add emotion fields to chat entity
  - sadness, happiness, anger, fear, love, like
  - Default values (neutral state)
- [ ] Update system prompt with emotion instructions
  - When to update emotions
  - How to format emotion changes
  - Examples of emotional responses
- [ ] Implement emotion parsing from structured output
- [ ] Validate emotion values (0-100 range)
- [ ] Update chat emotions in database

#### 3.2 Emotion Display UI

- [ ] Design custom SVG emotion icons
  - Sadness icon
  - Happiness icon
  - Anger icon
  - Fear icon
  - Love icon
  - Like icon
- [ ] Create emotion display component
  - Show current values
  - Visual indicators (icon + percentage/bar)
- [ ] Add emotion panel to chat view
  - Character profile card
  - Real-time updates
- [ ] Test emotion updates across conversation

**Milestone**: Emotions tracked and displayed, AI updates them contextually

---

## Phase 4: Memory & Context Intelligence (Week 8-10)

### Objectives

Implement sophisticated memory retrieval and context management.

### Tasks

#### 4.1 Event System

- [ ] Add event generation to structured output
- [ ] Update system prompt with event guidelines
  - What constitutes an event
  - How to write event descriptions
  - Event granularity examples
- [ ] Implement event storage
  - Save events from AI responses
  - Associate with chat and timestamp
- [ ] Test event detection quality
- [ ] Iterate on prompting

#### 4.2 Memory Retrieval Tools

- [ ] Implement `retrieveMessagesByKeyword` tool
  - Search message content
  - Return relevant excerpts
  - Rank by relevance
- [ ] Implement `retrieveMessagesByTimeRange` tool
  - Query by timestamp range
  - Return chronological results
- [ ] Implement `retrieveEventsByType` tool (future)
  - Categorize events (optional for MVP)
  - Search by category
- [ ] Add tools to AI context
  - Tool definitions
  - Usage examples in system prompt
- [ ] Test tool calling reliability

#### 4.3 Advanced Context Composition

- [ ] Implement plot summary generation
  - Periodic summary updates
  - AI-generated summaries
  - Store in chat metadata
- [ ] Update context composition logic
  - System prompt
  - Plot summary
  - Current emotions
  - Last 10 messages
  - Retrieved memories (if tools called)
  - User's current message
- [ ] Optimize context size
  - Token counting
  - Truncation strategies
  - Priority-based inclusion
- [ ] Test memory retrieval effectiveness
  - Does AI retrieve relevant info?
  - Does it improve response quality?

**Milestone**: Context management prevents rot, memory retrieval functional

---

## Phase 5: Multi-Modal Features (Week 11-13)

### Objectives

Add image generation and text-to-speech capabilities.

### Tasks

#### 5.1 Image Generation

- [ ] Implement image generation providers
  - Google Cloud (`google-cloud.ts` in `_core/services/ai/image`)
  - OpenRouter (`open-router.ts`)
- [ ] Create image generation UI
  - Image generation button
  - Description input modal
  - Loading skeleton (square animation)
- [ ] Implement AI-suggested image generation
  - Add to structured output
  - Quick action buttons in chat
- [ ] Store generated images
  - Blob URL creation
  - Save to IndexedDB media table
  - Associate with messages
- [ ] Display images in chat
  - Inline image viewer
  - Gallery view (optional)
  - Chronological placement
- [ ] Test image providers
- [ ] Handle generation errors

#### 5.2 Text-to-Speech

- [ ] Implement audio generation providers
  - ElevenLabs (`elevenlabs.ts` in `_core/services/ai/audio`)
  - Google Cloud (`google-cloud.ts`)
- [ ] Create audio playback system
  - Play button on each AI message
  - Audio player controls (play, pause, stop)
  - Volume control
- [ ] Implement voice configuration UI
  - Voice model selector
  - Speed adjustment
  - Pitch adjustment (if supported)
  - Volume slider
- [ ] Store audio files (optional)
  - MP3/OGG format
  - Save to IndexedDB (consider quota)
- [ ] Persist voice settings
  - Save per-generation
  - Carry over to next generations
- [ ] Test audio providers
- [ ] Handle generation errors

**Milestone**: Users can generate images and hear AI messages

---

## Phase 6: Settings & Configuration (Week 14)

### Objectives

Complete settings management and user preferences.

### Tasks

#### 6.1 API Key Management

- [ ] Create API key management UI
  - Provider selection
  - Key input fields
  - Multiple keys per provider
  - Active key indicator
- [ ] Implement key validation
  - Test API calls before saving
  - Clear error messages
  - Prevent invalid keys
- [ ] Store keys securely
  - Encryption/obfuscation in IndexedDB
  - Retrieve for API calls
- [ ] Provider/model selection UI
  - Dropdowns for each modality
  - List available models per provider
  - Persist selections

#### 6.2 User Preferences

- [ ] Create preferences UI
  - Context settings (number of recent messages)
  - UI preferences (theme, display format)
  - Default voice settings
- [ ] Implement preference persistence
  - Save to IndexedDB
  - Load on app start
  - Apply immediately (no reload)
- [ ] Validate preference values
  - Range checks
  - Prevent invalid states

**Milestone**: Users can configure all settings, API keys validated

---

## Phase 7: Polish & Testing (Week 15-16)

### Objectives

Refine UI/UX, complete testing, fix bugs.

### Tasks

#### 7.1 UI/UX Polish

- [ ] Responsive design tweaks
  - Desktop layout
  - Tablet layout
  - Mobile layout (basic)
- [ ] Loading states everywhere
  - Skeleton screens
  - Spinners
  - Progress indicators
- [ ] Error handling UI
  - Graceful error displays
  - Retry mechanisms
  - Help text
- [ ] Accessibility improvements
  - Keyboard navigation
  - ARIA labels
  - Focus management

#### 7.2 Testing Completion

- [ ] Achieve >80% coverage for database operations
- [ ] Test all provider implementations
- [ ] Integration tests for critical flows
- [ ] Manual testing of all features
- [ ] Cross-browser testing (Chrome, Firefox, Safari)

#### 7.3 Bug Fixes

- [ ] Prioritize critical bugs
- [ ] Fix data loss scenarios
- [ ] Fix provider switching issues
- [ ] Fix context management edge cases

#### 7.4 Documentation

- [ ] Update README with setup instructions
- [ ] Document API key acquisition process
- [ ] Create user guide (basic)
- [ ] Code documentation (JSDoc comments)

**Milestone**: MVP complete, tested, and polished

---

## Phase 8: Public Deployment (Week 17)

### Objectives

Deploy to Cloudflare Workers for public access.

### Tasks

#### 8.1 Deployment Setup

- [ ] Configure `wrangler.toml` for production
- [ ] Test build process
- [ ] Deploy to Cloudflare Workers
- [ ] Verify functionality in production

#### 8.2 Post-Deployment

- [ ] Monitor for errors
- [ ] Gather initial user feedback
- [ ] Create issue tracker (GitHub Issues)
- [ ] Plan first iteration based on feedback

**Milestone**: App publicly accessible and stable

---

## Future Phases (Post-MVP)

### Phase 9: Data Portability

- Export/import chat data (JSON format)
- Backup/restore functionality
- Cross-browser data transfer

### Phase 10: Character Templates

- Pre-built character library
- Character sharing/import
- Character customization tools

### Phase 11: Advanced Features

- Multi-character conversations
- Branching narratives
- Advanced emotion tracking (user-directed vs. general)
- Voice cloning for characters
- User-uploaded images

### Phase 12: Performance Optimization

- Service worker for offline support
- Advanced caching strategies
- Lazy loading for large chats
- Image compression
- Audio compression

### Phase 13: Monetization Preparation (Optional)

- Cloud-hosted version with managed API keys
- Subscription model research
- Premium feature definition
- Payment integration
- User authentication system

### Phase 14: Community Features

- Character marketplace
- Community templates
- Sharing functionality
- Collaboration features

---

## Risk Mitigation

### Technical Risks

| Risk                        | Impact | Mitigation                                             |
| --------------------------- | ------ | ------------------------------------------------------ |
| IndexedDB quota limits      | High   | Implement data cleanup, warn users, add export feature |
| Provider API changes        | Medium | Use contracts/interfaces, version lock SDKs            |
| Context window limits       | Medium | Implement smart truncation, test with long chats       |
| AI response quality         | High   | Iterate on prompts, provide examples, test extensively |
| Cross-browser compatibility | Medium | Test on major browsers, use standard APIs              |

### Product Risks

| Risk                       | Impact | Mitigation                                             |
| -------------------------- | ------ | ------------------------------------------------------ |
| User onboarding complexity | High   | Clear documentation, intuitive UI, tooltips            |
| API cost concerns          | Medium | Transparent about costs, show usage estimates          |
| Data loss (cache clear)    | High   | Warn users, provide export feature                     |
| Poor AI responses          | High   | Prompt engineering, model selection, iterative testing |

---

## Success Metrics

### MVP Success Criteria

- [ ] Users can complete full chat session without errors
- [ ] AI emotional responses are contextually appropriate (qualitative)
- [ ] Memory retrieval improves long conversations (qualitative)
- [ ] All three modalities (text, image, audio) work reliably
- [ ] No critical data loss bugs
- [ ] App loads and runs in all major browsers

### Post-MVP Goals

- 10+ active testers providing feedback
- Average chat length >20 messages
- <5% error rate in provider API calls
- Positive qualitative feedback on AI response quality
- Evidence of users returning to previous chats

---

## Version History

| Version | Date         | Changes                 |
| ------- | ------------ | ----------------------- |
| 1.0     | Feb 16, 2026 | Initial roadmap created |
