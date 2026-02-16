# Product Requirements Document (PRD)

## AI Roleplay Chat Application

**Version:** 1.0  
**Last Updated:** February 16, 2026  
**Status:** Initial Development

> **For AI Assistants**: This PRD defines the complete functional and technical specification for an AI-powered roleplay chat application. Key architectural principles: client-side first, provider-agnostic design, sophisticated context management, multi-modal capabilities. Reference this document for implementation decisions, architecture questions, and feature specifications. Companion document: `ROADMAP.md` for development phases and priorities.

### Quick Reference

| Category             | Details                                                                                                      |
| -------------------- | ------------------------------------------------------------------------------------------------------------ |
| **Tech Stack**       | Vanilla TypeScript, Vite, Vitest, TailwindCSS, IndexedDB                                                     |
| **Deployment**       | Cloudflare Workers (public access only)                                                                      |
| **AI Providers**     | Text: OpenAI, Google Cloud, OpenRouter<br>Image: Google Cloud, OpenRouter<br>Audio: ElevenLabs, Google Cloud |
| **Storage**          | IndexedDB (client-side, browser-dependent)                                                                   |
| **Testing Priority** | Database operations, Provider contracts, Unit tests                                                          |
| **Core Features**    | Chat with emotions, Memory retrieval, Image generation, TTS                                                  |
| **Architecture**     | Modules (business logic) + Services (generic abstractions) + UI (presentation)                               |

### Document Navigation

- **Section 3**: Functional requirements (what the app does)
- **Section 4**: Non-functional requirements (how it should work)
- **Section 5**: Architecture & project structure
- **Section 6**: AI integration specifics (prompts, context, tools)
- **Section 11**: Open questions & decisions
- **Section 13**: Implementation notes for AI assistants
- **Appendix B**: Example user flows

**Related Documents**: `ROADMAP.md` (development phases), `README.md` (setup guide)

---

## 1. Project Overview

### 1.1 Product Description

An AI-powered chat application designed for immersive roleplay experiences. Users interact with AI characters through sophisticated context management, emotion tracking, and multi-modal capabilities (text, images, audio).

### 1.2 Target Audience

- AI enthusiasts interested in character-based interactions
- Users seeking immersive roleplay experiences
- Individuals interested in creative AI conversations

### 1.3 Core Differentiators

- **Sophisticated Context Management**: Prevents context rot through intelligent memory retrieval
- **Emotion Tracking**: AI maintains emotional state throughout conversations
- **Multi-modal Interaction**: Text, image generation, and text-to-speech capabilities
- **Provider Flexibility**: Support for multiple AI providers with hot-swapping
- **Client-side First**: Runs entirely in the browser with user-provided API keys

---

## 2. Technical Stack

### 2.1 Core Technologies

- **Framework**: Vanilla TypeScript
- **Build Tool**: Vite
- **Testing**: Vitest
- **Styling**: TailwindCSS
- **Database**: IndexedDB (client-side storage)
- **Deployment**: Cloudflare Workers (for public access only)

### 2.2 External Dependencies

- AI provider SDKs/clients (OpenAI, Google Cloud, OpenRouter, ElevenLabs)
- Markdown parser
- IndexedDB wrapper/utilities

### 2.3 Out of Scope (Initial Version)

- CI/CD pipelines
- Performance optimization
- Enterprise-level scaling
- Backend services
- User authentication
- Cloud-hosted API keys

---

## 3. Functional Requirements

### 3.1 Chat Management

#### 3.1.1 Chat Creation

**Description**: Users can create new chat sessions with AI characters.

**User Flow**:

1. User clicks "New Chat"
2. User provides:
   - Chat plot/scenario description
   - AI character details (name, personality, background)
   - User's character details (name, role in scenario)
3. System initializes chat context
4. User can either:
   - Prompt AI to send first message, OR
   - Send first message themselves
5. Conversation begins

**Acceptance Criteria**:

- Each chat is independent and isolated
- Character information is stored in chat context
- Initial plot is stored for context retrieval
- User can start conversation either way (AI-first or user-first)

#### 3.1.2 Message Exchange

**Description**: Users exchange messages with AI characters.

**Message Types**:

- Text content
- Actions/narration (e.g., _smiles_, _walks away_)

**Acceptance Criteria**:

- Messages display in chronological order
- User and AI messages are visually distinct
- Message history persists in IndexedDB
- Messages support markdown formatting
- Actions/narration are properly rendered

#### 3.1.3 Chat History

**Description**: Users can view and access previous chats.

**Acceptance Criteria**:

- All chats are saved automatically
- Users can browse chat list
- Users can resume previous chats
- Users can delete chats
- Chat metadata includes: title, last message timestamp, character name

### 3.2 Context & Memory Management

#### 3.2.1 Context Strategy

**Description**: Intelligent context management to prevent context rot and optimize AI responses.

**Components**:

1. **Recent Messages**: Last N messages sent directly to AI
2. **Plot Summary**: Main scenario/story summary maintained throughout chat
3. **Event Table**: Short descriptions of significant events
4. **Memory Retrieval**: AI can call functions to retrieve older messages/events

**Memory Retrieval Mechanism**:

- AI automatically decides when to retrieve old information
- Function calling interface for memory queries
- Search by keywords, time range, or event type
- Retrieved memories are injected into context for current response

**Acceptance Criteria**:

- Context size remains manageable (doesn't grow infinitely)
- AI can access relevant historical information when needed
- Event descriptions are auto-generated and stored
- Plot summary updates based on conversation progress
- Memory retrieval is transparent to user

#### 3.2.2 Emotion Tracking

**Description**: AI maintains emotional state throughout conversation.

**Emotion Stats** (tracked per AI character):

- Sadness
- Happiness
- Anger
- Fear
- Love
- Like

**Update Mechanism**:

- AI updates emotions via structured output with each response
- Emotions can remain unchanged (blank/null in structured output)
- Changes reflect conversation context and events

**Acceptance Criteria**:

- Emotions display in AI character profile (visible in UI)
- Emotions persist across conversation
- AI instructions clearly explain how to update emotions
- Structured output validation ensures valid emotion values
- UI updates reflect emotion changes in real-time

**Future Considerations**:

- Separate emotions toward user vs. general mood
- More granular emotion types
- Emotion history/timeline view

### 3.3 Multi-modal Capabilities

#### 3.3.1 Image Generation

**Description**: Users can generate images to illustrate scenes or characters.

**Trigger Methods**:

1. **Manual**: User clicks image icon and provides description
2. **AI Suggestion**: AI sends quick action button suggesting image generation

**User Flow**:

1. User clicks image generation button (or AI suggests it)
2. User provides/confirms description (e.g., "how X character looks right now")
3. System calls image generation API
4. Generated image appears in chat history at prompt position
5. Image is stored in IndexedDB

**Acceptance Criteria**:

- Images appear inline in chat history
- Images maintain chronological order with messages
- Users can provide custom descriptions
- AI can suggest contextually relevant images
- Generated images are cached locally
- Users can select image provider/model

#### 3.3.2 Text-to-Speech (TTS)

**Description**: Users can hear AI messages spoken aloud.

**Features**:

- Every AI message has a "play" button
- Users can configure voice settings per generation
- Settings persist for subsequent generations
- No speech-to-text (user input is text-only)

**Voice Configuration**:

- Voice type/model selection
- Speed/rate adjustment
- Pitch adjustment (if supported by provider)
- Volume control

**User Flow**:

1. AI sends message
2. User clicks play button on message
3. (Optional) User adjusts voice settings
4. Audio generates and plays
5. Settings save for next generation

**Acceptance Criteria**:

- Play button appears on all AI messages
- Audio generation is on-demand (not automatic)
- Voice settings UI is accessible and intuitive
- Settings persist across sessions
- Audio playback controls (play, pause, stop)
- Audio caching (optional, nice-to-have)

### 3.4 AI Provider Management

#### 3.4.1 Multi-Provider Support

**Description**: Support for multiple AI providers with ability to switch at any time.

**Supported Providers**:

- **Text Generation**: OpenAI, Google Cloud, OpenRouter
- **Image Generation**: Google Cloud, OpenRouter
- **Audio Generation**: ElevenLabs, Google Cloud

**Architecture Requirements**:

- Abstract provider interface (contract)
- High dependency injection and modularity
- Provider-agnostic business logic
- Easy addition of new providers

**Acceptance Criteria**:

- Users can switch providers mid-conversation
- Provider interface is consistent across implementations
- Model selection is provider-specific
- Each provider can have different model options
- Error handling is provider-agnostic

#### 3.4.2 API Key Management

**Description**: Users configure and manage their own API keys.

**Features**:

- Multiple keys per provider
- User selects active key per provider
- Key validation before saving
- Secure storage in IndexedDB

**User Flow**:

1. User navigates to Settings
2. Selects provider
3. Adds API key
4. System validates key (test API call)
5. User selects which key to use (if multiple exist)
6. Key saves and activates

**Acceptance Criteria**:

- Keys are encrypted/obfuscated in IndexedDB
- Validation occurs before saving
- Invalid keys show clear error messages
- Users can add/edit/delete keys
- Active key is clearly indicated
- Keys are provider-specific
- Test validation call doesn't consume significant credits

### 3.5 Settings & Preferences

#### 3.5.1 User Preferences

**Description**: Configurable settings for user experience.

**Settings Categories**:

- **AI Provider Settings**:
  - Active provider per modality (text, image, audio)
  - Active model per provider
  - Active API key per provider
- **Voice/TTS Settings**:
  - Default voice model
  - Speed, pitch, volume defaults
- **UI Preferences**:
  - Theme (light/dark)
  - Message display format
  - Timestamp visibility
- **Context Settings**:
  - Number of recent messages to include
  - Memory retrieval sensitivity
  - Event summary frequency

**Acceptance Criteria**:

- All settings persist in IndexedDB
- Settings apply immediately (no reload required)
- Settings are exportable (future consideration)
- Clear defaults for all settings
- Settings validation prevents invalid states

---

## 4. Non-Functional Requirements

### 4.1 Data Persistence

#### 4.1.1 IndexedDB Storage

**Data to Store**:

1. **Chats**:
   - Chat metadata (id, title, created_at, updated_at)
   - Character details (AI and user)
   - Plot summary
   - Emotion state
2. **Messages**:
   - Message content
   - Sender (user/AI)
   - Timestamp
   - Structured output (emotions, tool calls)
3. **Events**:
   - Event descriptions
   - Associated chat_id
   - Timestamp
4. **Media**:
   - Generated images (base64 or blob)
   - Generated audio (optional, nice-to-have)
   - Associated message_id
5. **User Preferences**:
   - All settings from section 3.5.1
6. **API Keys**:
   - Provider name
   - Key value (encrypted)
   - Active status
   - Label/name

**Acceptance Criteria**:

- Database schema supports all data types
- Efficient querying for chat history
- Data survives browser sessions
- Database handles large chat histories
- Proper indexing for performance

#### 4.1.2 Data Lifecycle

**Behavior**:

- Data clears when user clears browser cache (acceptable limitation)
- Users switching browsers lose data (acceptable limitation)

**Future Consideration**:

- Export/import functionality for data portability

### 4.2 Testing Strategy

#### 4.2.1 Unit Testing

**Priority**: High

**Test Coverage**:

- Database operations (CRUD for all entities)
- AI provider contracts/interfaces
- Utility functions (markdown parser, alerts, etc.)
- Context management logic
- Emotion state updates
- Memory retrieval algorithms

**Framework**: Vitest

**Acceptance Criteria**:

- Critical paths have >80% coverage
- All database operations tested
- All provider implementations follow contract
- Mock external API calls

#### 4.2.2 Integration Testing

**Priority**: Medium

**Test Coverage**:

- Provider integration with real API calls (optional, test environment)
- Database + business logic integration
- Multi-module workflows

**Acceptance Criteria**:

- Provider contracts work with real implementations
- Database transactions are atomic
- Error handling across module boundaries

#### 4.2.3 End-to-End Testing

**Priority**: Low (future consideration)

**Future Scope**:

- Full user flows
- UI interactions
- Multi-modal workflows

---

## 5. Project Structure & Architecture

### 5.1 Directory Organization

```
src/
├── _core/
│   ├── modules/          # Business logic for features
│   │   ├── chat/         # Chat-specific business rules
│   │   ├── settings/     # Settings management
│   │   └── [future modules for characters, memory, etc.]
│   │
│   ├── services/         # Generic, reusable services
│   │   ├── ai/           # AI provider abstractions
│   │   │   ├── text/     # Text generation providers
│   │   │   ├── image/    # Image generation providers
│   │   │   └── audio/    # TTS providers
│   │   ├── db/           # IndexedDB wrapper
│   │   └── audio/        # Audio playback utilities
│   │
│   └── utils/            # Utility functions
│       ├── alert.ts      # User notifications
│       └── mdParser.ts   # Markdown parsing
│
├── _ui/
│   ├── base/             # CSS foundation
│   │   ├── global.css    # Global styles
│   │   ├── theme.css     # Theming variables
│   │   └── components.css # Base component styles
│   │
│   ├── components/       # UI components (vanilla TS)
│   └── main.css          # Main stylesheet entry
│
└── _public/              # Static assets
```

### 5.2 Architecture Principles

#### 5.2.1 Separation of Concerns

- **Modules**: Feature-specific business logic (chat, settings, etc.)
- **Services**: Generic, reusable functionality (AI, database, audio)
- **UI**: Presentation layer only
- **Utils**: Pure functions with no side effects

#### 5.2.2 Dependency Injection

- AI providers implement contracts/interfaces
- Services are injected into modules
- Easy to swap implementations
- Facilitates testing with mocks

#### 5.2.3 Provider Contracts

**Text Generation Interface**:

```typescript
interface TextGenerationProvider {
  generateResponse(params: GenerationParams): Promise<GenerationResult>;
  validateApiKey(key: string): Promise<boolean>;
  listModels(): Promise<Model[]>;
}
```

**Image Generation Interface**:

```typescript
interface ImageGenerationProvider {
  generateImage(params: ImageParams): Promise<ImageResult>;
  validateApiKey(key: string): Promise<boolean>;
  listModels(): Promise<Model[]>;
}
```

**Audio Generation Interface**:

```typescript
interface AudioGenerationProvider {
  generateAudio(params: AudioParams): Promise<AudioResult>;
  validateApiKey(key: string): Promise<boolean>;
  listVoices(): Promise<Voice[]>;
}
```

#### 5.2.4 Database Abstraction

- Base repository pattern for CRUD operations
- Entity-specific repositories extend base
- Typed interfaces for all entities
- Transaction support for complex operations

**Example**:

```typescript
class BaseRepository<T> {
  create(entity: T): Promise<T>;
  read(id: string): Promise<T>;
  update(id: string, entity: Partial<T>): Promise<T>;
  delete(id: string): Promise<void>;
  list(filters?: Filters): Promise<T[]>;
}

class ChatRepository extends BaseRepository<Chat> {
  // Chat-specific methods
  getWithMessages(chatId: string): Promise<ChatWithMessages>;
  updateEmotions(chatId: string, emotions: Emotions): Promise<void>;
}
```

### 5.3 State Management

**Approach**: Vanilla TypeScript with event-driven updates

**Key Patterns**:

- Custom event system for cross-module communication
- LocalStorage for UI state (optional)
- IndexedDB for persistent data
- In-memory state for active chat session

---

## 6. AI Integration Details

### 6.1 Structured Output Format

**Purpose**: AI returns structured data for emotions and tool calls.

**Example Response Schema**:

```typescript
interface AIResponse {
  message: string; // Actual message content
  emotions?: {
    // Updated emotions (null if no change)
    sadness?: number; // 0-100
    happiness?: number;
    anger?: number;
    fear?: number;
    love?: number;
    like?: number;
  };
  toolCalls?: ToolCall[]; // Memory retrieval requests
  suggestedActions?: {
    // Quick action buttons
    generateImage?: {
      description: string;
    };
  };
}
```

### 6.2 Memory Retrieval Tools

**Available Functions**:

1. **retrieveMessagesByKeyword**:
   - Search messages containing specific keywords
   - Returns relevant message excerpts
2. **retrieveMessagesByTimeRange**:
   - Fetch messages from specific time period
   - Useful for "remember when..." queries
3. **retrieveEventsByType**:
   - Search event table by category
   - Returns event descriptions

**System Prompt Instructions**:

- Explain when to use memory retrieval
- Provide examples of good queries
- Encourage retrieval for better responses
- Explain context limitations

### 6.3 Context Composition

**Context Structure Sent to AI**:

```
1. System Prompt (character definition, emotion tracking, tools)
2. Plot Summary
3. Current Emotion State
4. Recent Messages (last N messages)
5. Retrieved Memories (if AI called retrieval tools)
6. User's Latest Message
```

**Context Optimization**:

- Keep recent messages count configurable
- Truncate old messages intelligently
- Prioritize retrieved memories over chronological history
- Update plot summary periodically based on events

---

## 7. User Interface Requirements

### 7.1 Core Views

#### 7.1.1 Chat List View

**Components**:

- List of all chats (sorted by last message)
- New chat button
- Search/filter chats
- Chat preview (last message, character name)

#### 7.1.2 Chat View

**Components**:

- Message thread
- Input area (text field + send button)
- AI character profile card (with emotion display)
- Action buttons:
  - Generate image
  - Play audio (per message)
  - Model/provider selector

**Layout**:

- Character profile: sidebar or collapsible panel
- Messages: scrollable center area
- Input: fixed bottom bar

#### 7.1.3 Settings View

**Sections**:

- API Key Management
- Provider Selection
- Voice/TTS Configuration
- UI Preferences
- Context Settings

#### 7.1.4 New Chat Modal

**Fields**:

- Plot/scenario description (textarea)
- AI character details (name, personality, background)
- User character details (name, role)
- (Optional) Initial message choice

### 7.2 UI Components (Vanilla TS)

**Required Components**:

- Message bubble (user/AI variants)
- Emotion display widget
- Audio player controls
- Image viewer/gallery
- Provider selector dropdown
- API key input with validation
- Quick action buttons (AI suggestions)
- Modal/dialog system
- Toast/alert notifications

### 7.3 Responsive Design

**Priority**: Medium (desktop-first, mobile-friendly)

**Breakpoints**:

- Desktop: 1024px+
- Tablet: 768px - 1023px
- Mobile: < 768px

---

## 8. Error Handling

### 8.1 API Errors

**Scenarios**:

- Invalid API key
- Rate limiting
- Network failures
- Model unavailable
- Insufficient credits

**User Experience**:

- Clear error messages
- Suggested fixes (e.g., "Check API key in settings")
- Graceful degradation (don't lose user's message)
- Retry mechanism for transient errors

### 8.2 Database Errors

**Scenarios**:

- IndexedDB quota exceeded
- Corrupted data
- Migration failures

**User Experience**:

- Data backup prompts before risky operations
- Clear storage usage indicators
- Export data option before clearing

### 8.3 Validation Errors

**Scenarios**:

- Empty messages
- Invalid emotion values
- Malformed structured output from AI

**User Experience**:

- Inline validation messages
- Prevent invalid submissions
- Fallback values for AI errors

---

## 9. Future Considerations

**Note**: Detailed roadmap and feature prioritization are documented in `ROADMAP.md`.

### 9.1 Deferred Features

- Export/import chat data functionality
- Character template library
- Advanced emotion tracking refinements
- Multi-character conversations
- User-uploaded images

### 9.2 Technical Debt

- Performance optimization for large chat histories
- Advanced caching strategies
- Service worker for offline support

---

## 10. Success Criteria

### 10.1 MVP Definition

The MVP is complete when:

- Users can create chats with custom characters and plots
- AI responds with contextual emotion tracking
- Memory retrieval system functions automatically
- Image generation works from user prompts and AI suggestions
- TTS plays AI messages with configurable voices
- Multiple providers are supported and switchable
- API keys are manageable and validated
- Data persists across browser sessions
- Database and provider integrations have >80% test coverage

### 10.2 Quality Gates

Before public deployment:

- No critical bugs (data loss, app crashes)
- Provider switching works seamlessly
- Memory retrieval demonstrably improves conversation quality
- All three modalities (text, image, audio) are reliable
- UI is intuitive (based on tester feedback)

**See `ROADMAP.md` for detailed success metrics and milestones.**

---

## 11. Constraints & Limitations

### 11.1 Known Limitations

- **Client-side only**: No server, no cross-device sync
- **Browser dependency**: Data tied to browser storage
- **API costs**: User bears all API costs
- **No authentication**: Anyone with link can access (Cloudflare deployment)
- **No collaboration**: Single-user experience only

### 11.2 Acceptable Trade-offs

- Data loss on cache clear (documented, user-accepted risk)
- No real-time features (acceptable for MVP)
- Limited testing (E2E deferred to later)
- Performance not optimized initially (local-first approach)

---

## Appendix A: Glossary

- **Context Rot**: Degradation of AI response quality due to excessively long context windows
- **Structured Output**: AI responses formatted as JSON/typed objects
- **Tool Calling**: AI's ability to invoke functions (e.g., memory retrieval)
- **Event Table**: Database table storing short descriptions of significant conversation events
- **Plot Summary**: Condensed narrative of the chat's main storyline
- **Emotion Stats**: Numerical representations of AI character's emotional state
- **Provider**: External API service (OpenAI, Google Cloud, etc.)
- **Modality**: Type of AI capability (text, image, audio)

---

## Appendix B: Example User Flows

### B.1 Complete Chat Session Flow

1. User clicks "New Chat"
2. Fills in plot: "A detective and their AI partner solve crimes in a cyberpunk city"
3. AI character: "NEXUS, a sarcastic AI embedded in the detective's neural implant"
4. User character: "Detective Riley, a jaded veteran cop"
5. User prompts AI to send first message
6. AI responds: "Another body, Riley. You know, for someone who hates working nights, you sure pick up a lot of night shift cases."
7. Emotion display shows: Happiness: 30, Like: 40 (sarcastic tone)
8. User replies: "Just lucky I guess. What do we know about the victim?"
9. AI retrieves context about previous cases (tool call)
10. AI responds with case details, emotions update: Happiness: 25, Like: 45
11. User clicks image icon: "Show me the crime scene"
12. Image generates and appears in chat
13. User clicks play button on AI's last message
14. Configures voice to be "robotic, slightly sarcastic"
15. Audio plays
16. Conversation continues...

### B.2 Provider Switching Flow

1. User mid-conversation with OpenAI GPT-4
2. Clicks provider selector
3. Switches to Google Cloud Gemini
4. Next AI response uses new provider
5. Conversation context maintained
6. Emotion state preserved
7. No interruption to user experience

---

## 12. Implementation Notes for AI Assistants

### 12.1 Code Generation Guidelines

When implementing features from this PRD:

1. **Always follow the established folder structure** (`_core/modules/`, `_core/services/`, `_ui/`)
2. **Respect separation of concerns**: Modules contain business logic, Services are generic abstractions
3. **Use TypeScript strictly**: Explicit types for all interfaces, no `any` unless absolutely necessary
4. **Provider implementations must follow contracts**: See section 5.2.3 for interface definitions
5. **Database operations use repository pattern**: See section 5.2.4 for base repository structure
6. **Test database and provider code**: Priority testing areas per section 4.2

### 12.2 Key Constraints to Remember

- **No external libraries for markdown parsing or IndexedDB** - custom implementations required
- **Audio format**: MP3 or OGG only
- **Image storage**: Blob URLs, not base64
- **Context window**: Default 10 recent messages
- **No backend/server-side code**: Entirely client-side application
- **No deployment optimization yet**: Focus on functionality first

### 12.3 Critical Architecture Patterns

```typescript
// Provider contract example
interface TextGenerationProvider {
  generateResponse(params: GenerationParams): Promise<GenerationResult>;
  validateApiKey(key: string): Promise<boolean>;
  listModels(): Promise<Model[]>;
}

// Repository pattern example
class BaseRepository<T> {
  async create(entity: T): Promise<T> {
    /* ... */
  }
  async read(id: string): Promise<T> {
    /* ... */
  }
  async update(id: string, partial: Partial<T>): Promise<T> {
    /* ... */
  }
  async delete(id: string): Promise<void> {
    /* ... */
  }
  async list(filters?: Filters): Promise<T[]> {
    /* ... */
  }
}

// Structured AI response format
interface AIResponse {
  message: string;
  emotions?: {
    sadness?: number; // 0-100
    happiness?: number;
    anger?: number;
    fear?: number;
    love?: number;
    like?: number;
  };
  toolCalls?: ToolCall[];
  suggestedActions?: { generateImage?: { description: string } };
  events?: string[]; // New events to add to event table
}
```

### 12.4 Prompt Engineering Context

AI models in this application must:

- Track 6 emotion stats (section 3.2.2)
- Use tool calling for memory retrieval (section 6.2)
- Generate event descriptions automatically (section 11.1)
- Return structured output matching `AIResponse` interface (section 6.1)
- Understand context composition strategy (section 6.3)

Prompt design is intentionally iterative - see section 11.3.
