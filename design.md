# Design Document

## Design Overview

Thread.ai is architected as a multimodal AI learning platform that accelerates technical understanding through intelligent context integration. The system's core design principle centers on reducing cognitive load by automatically connecting user queries with relevant context from documents, images, and conversation history.

The architecture supports faster learning through three key mechanisms:
1. **Context Continuity**: Maintains conversation threads and document relationships to eliminate repetitive context switching
2. **Multimodal Understanding**: Processes text, images, and documents simultaneously to provide comprehensive answers
3. **Adaptive Response Delivery**: Offers both text and video responses based on content complexity and user preference

Multimodal AI is essential because technical learning often involves visual elements (code screenshots, architecture diagrams, error messages) that pure text-based systems cannot effectively process. By combining conversational AI with computer vision and document understanding, Thread.ai eliminates the friction of manually describing visual content to get relevant help.

## System Architecture

### Frontend
- React-based single-page application
- Responsive design supporting desktop, tablet, and mobile
- Real-time WebSocket connections for streaming responses
- Browser-based file upload and processing
- Web Speech API integration for voice interactions

### Backend Services
- Node.js/Express API server handling HTTP requests and WebSocket connections
- Stateless microservice architecture for horizontal scaling
- Session-based memory management without persistent user data storage
- RESTful endpoints for file upload, processing, and response generation

### AI Orchestration Layer
- Central coordinator managing AI model interactions
- Request routing based on content type and complexity
- Response aggregation from multiple AI services
- Cost optimization through intelligent API call batching

### Data Flow Overview
User input flows through the frontend to the orchestration layer, which determines the appropriate AI services to engage. Document uploads are processed through OCR and text extraction, then indexed in the RAG pipeline. Images are analyzed through the vision pipeline. The conversational engine receives enriched context from both pipelines and generates responses. For video requests, text responses are passed to the avatar generation system. All processing occurs in-memory with no persistent storage of user content.

## Core Components

### Conversational Intelligence Engine

**Purpose**: Provides contextually aware conversational AI that maintains learning continuity and adapts explanations based on user comprehension.

**Inputs**:
- User text queries
- Conversation history
- Enriched context from RAG and vision pipelines
- User preference indicators (complexity level, response format)

**Outputs**:
- Natural language responses with technical explanations
- Follow-up questions for clarification
- Structured learning recommendations
- Source citations and references

**Design Rationale**: Using Google's Gemini model provides strong reasoning capabilities while maintaining cost efficiency. The engine improves learning by maintaining conversation context, allowing users to build understanding incrementally without repeating background information. Adaptive explanation complexity helps users at different skill levels engage effectively with technical content.

### Knowledge Retrieval (RAG Pipeline)

**Purpose**: Extracts, indexes, and retrieves relevant information from user-uploaded documents to ground AI responses in specific user context.

**Inputs**:
- PDF, text, markdown, and code files
- User queries requiring document-specific information
- Document metadata and structure information

**Outputs**:
- Relevant document excerpts with source citations
- Semantic similarity scores for content ranking
- Structured document summaries
- Cross-document relationship mappings

**Design Rationale**: Pinecone vector database provides fast semantic search across document content. This design choice dramatically improves productivity by eliminating manual document searching. Users can ask questions about their specific codebase, documentation, or research materials and receive precise, cited answers. The RAG approach ensures responses are grounded in user-provided context rather than generic information.

### Vision Intelligence Pipeline

**Purpose**: Analyzes visual content including code screenshots, diagrams, charts, and technical images to extract actionable information.

**Inputs**:
- Image files (PNG, JPEG, SVG)
- Screenshots of code, error messages, or interfaces
- Technical diagrams and architectural drawings
- Charts and data visualizations

**Outputs**:
- Structured text descriptions of visual content
- Extracted code with syntax highlighting
- Diagram relationship mappings
- OCR text extraction with confidence scores

**Design Rationale**: Combining TensorFlow-based OCR with Gemini's vision capabilities provides robust visual understanding. This significantly improves learning efficiency by allowing users to share screenshots instead of manually transcribing code or describing complex diagrams. The pipeline handles common developer workflows like debugging from screenshots or understanding system architecture diagrams.

### Attachment Processing

**Purpose**: Coordinates the ingestion and processing of multiple file types to create unified context for AI reasoning.

**Inputs**:
- Mixed file uploads (documents, images, code files)
- File metadata and type information
- Processing priority indicators

**Outputs**:
- Unified context objects combining all attachment information
- Processing status and error handling
- Content type classifications and routing decisions

**Design Rationale**: Centralized attachment processing ensures consistent handling across file types and enables cross-modal reasoning. This design improves productivity by allowing users to upload entire project contexts (code + documentation + screenshots) and receive comprehensive assistance that considers all available information.

### Avatar Video Response System

**Purpose**: Generates video-based explanations with AI avatars for complex topics that benefit from visual and auditory presentation.

**Inputs**:
- Text responses from conversational engine
- Content complexity indicators
- User avatar and presentation preferences
- Accessibility requirements (captions, descriptions)

**Outputs**:
- MP4 video files with AI avatar presentations
- Synchronized audio narration
- Captions and transcripts
- Visual aids and gesture coordination

**Design Rationale**: Gooey AI provides cost-effective avatar generation without requiring specialized hardware. Video responses improve learning for complex topics by engaging multiple senses and providing visual cues. This is particularly valuable for explaining abstract concepts, code architecture, or step-by-step procedures where visual demonstration enhances understanding.

## AI Workflow (End-to-End)

### Step 1: User Input Processing
User submits query through web interface, potentially with attached files. Frontend validates input and establishes WebSocket connection for streaming responses.

### Step 2: Context Construction
Orchestration layer analyzes input type and routes attachments to appropriate processors. Documents flow through RAG pipeline for indexing, images through vision pipeline for analysis. Context manager aggregates all available information into unified context object.

### Step 3: Model Interaction
Conversational engine receives enriched context and generates response using Gemini model. System includes relevant document excerpts, image analysis results, and conversation history in the prompt to ensure grounded, contextual responses.

### Step 4: Response Grounding
Generated response is validated against source materials and enhanced with citations. System checks for factual consistency with uploaded documents and flags any potential discrepancies.

### Step 5: Multimodal Output Delivery
Based on content complexity and user preference, response is delivered as text or routed to avatar generation system for video creation. All responses include source citations and follow-up suggestions to maintain learning momentum.

## Technology Choices & Rationale

### Gemini
Chosen for strong multimodal capabilities and cost-effective pricing. Provides excellent reasoning for technical content while supporting both text and image inputs in a single API call, reducing system complexity.

### Pinecone
Vector database selection based on managed service benefits and semantic search performance. Eliminates infrastructure management overhead while providing fast, accurate document retrieval essential for RAG effectiveness.

### Gooey AI
Selected for avatar generation due to API simplicity and cost efficiency. Enables video response features without requiring GPU infrastructure or complex video processing pipelines.

### TensorFlow / OCR
Provides reliable text extraction from images with good accuracy for code and technical diagrams. Open-source approach reduces API costs for high-volume image processing.

### Web Speech API
Browser-native speech recognition eliminates additional service dependencies while providing good accuracy for voice input. Reduces system complexity and improves privacy by processing speech locally.

### React Frontend
Industry-standard choice providing robust ecosystem, component reusability, and excellent developer experience. Supports responsive design requirements and integrates well with real-time features.

## Scalability & Extensibility

### Model Integration
System architecture supports pluggable AI models through standardized interfaces. New models can be integrated by implementing the common interface contract, allowing for easy experimentation and optimization.

### Enterprise vs Individual Modes
Architecture supports both deployment modes through configuration-driven feature flags. Enterprise deployments can enable additional security features, custom model endpoints, and enhanced analytics while maintaining the same core codebase.

### GPU Acceleration
Current browser-based architecture can be extended with optional GPU acceleration for vision processing. WebGL and WebGPU support can be added incrementally without disrupting existing functionality.

### Horizontal Scaling
Stateless microservice design enables horizontal scaling through load balancers. Session data stored in Redis allows for seamless request distribution across multiple server instances.

## Security & Privacy Considerations

### Session-Based Memory
All user data exists only in active session memory, automatically purged when sessions end. This approach eliminates long-term data storage risks while maintaining necessary context for learning continuity.

### No Long-Term File Storage
Uploaded files are processed in-memory and discarded after session completion. This design choice prioritizes user privacy over system optimization, ensuring sensitive documents cannot be accessed after use.

### Controlled Document Access
Document processing occurs in isolated containers with no network access to external services beyond necessary AI APIs. This prevents unauthorized data exfiltration while maintaining functionality.

### Encryption in Transit
All data transmission uses TLS 1.3 encryption with certificate pinning to prevent man-in-the-middle attacks. WebSocket connections maintain the same security standards as HTTP requests.

## Limitations & Trade-offs

### Latency vs Realism
Avatar video generation introduces 15-30 second delays compared to instant text responses. This trade-off prioritizes response quality and realism over speed, suitable for complex explanations where the additional wait time is justified by improved comprehension.

### Non-Real-Time Video
Current architecture generates pre-recorded video responses rather than real-time avatar interactions. This limitation reduces infrastructure complexity and costs while still providing multimodal learning benefits.

### Browser Compute Limits
Client-side processing is constrained by browser capabilities and device performance. Heavy computational tasks are offloaded to server-side processing, which may increase latency but ensures consistent performance across devices.

### Memory Constraints
Session-based architecture limits the amount of context that can be maintained simultaneously. Large document collections may require selective processing rather than comprehensive indexing.

## Future Enhancements

### Real-Time Avatars
WebRTC integration could enable real-time avatar conversations, reducing response latency and enabling more natural interactions. This would require significant infrastructure investment in GPU-accelerated video processing.

### GPU Vision Pipelines
Dedicated GPU infrastructure could accelerate image processing and enable more sophisticated computer vision capabilities, including real-time video analysis and advanced diagram understanding.

### Custom Voices
Voice cloning technology could allow users to customize avatar voices for personalized learning experiences. This enhancement would require additional privacy considerations and user consent mechanisms.

### On-Device Inference
WebAssembly and WebGPU could enable local model inference for basic queries, reducing API costs and improving privacy. This approach would be particularly valuable for code completion and simple question answering.

### Advanced Context Management
Implementing persistent context graphs could maintain learning relationships across sessions while preserving privacy through local storage and encryption.