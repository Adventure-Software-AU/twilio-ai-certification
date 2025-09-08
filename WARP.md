# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is a voice assistant application built with Node.js, Twilio Voice API, ConversationRelay, and OpenAI API. The application enables two-way conversations over phone calls through a WebSocket-based real-time communication system.

## Development Commands

### Setup and Installation
```bash
# Install dependencies
npm install

# Set up environment variables
cp .env.example .env
# Edit .env file to add OPENAI_API_KEY and NGROK_URL
```

### Running the Application
```bash
# Start the development server
node server.js

# Start ngrok tunnel (required for Twilio webhooks)
ngrok http 8080
```

### Testing
Currently no test suite is configured. Tests would need to be added to package.json scripts.

## Architecture

### Core Components

**Single-File Architecture**: The entire application is contained in `server.js`, implementing a Fastify-based HTTP/WebSocket server.

**Key Architectural Patterns**:
- **WebSocket Communication**: Uses Twilio ConversationRelay for bidirectional voice-to-text communication
- **Session Management**: In-memory session storage using Map to track conversation history per call
- **Stateful Conversations**: Maintains conversation context throughout each phone call session

### API Integration Flow

1. **Twilio Integration**: `/twiml` endpoint returns TwiML with ConversationRelay configuration
2. **WebSocket Handler**: `/ws` endpoint manages real-time communication with Twilio
3. **OpenAI Processing**: Processes voice prompts through GPT-4o-mini model
4. **Response Flow**: AI responses are sent back through WebSocket to be converted to speech

### Message Types
- `setup`: Initializes call session with conversation history
- `prompt`: Processes user voice input and generates AI response
- `interrupt`: Handles conversation interruptions (placeholder implementation)

## Configuration

### Environment Variables
- `OPENAI_API_KEY`: Required OpenAI API key for GPT model access
- `NGROK_URL`: Required ngrok forwarding URL (without protocol) for Twilio webhooks
- `PORT`: Optional server port (defaults to 8080)

### Twilio Configuration
Phone number webhook must be configured to: `https://[your-ngrok-subdomain].ngrok.app/twiml`

### AI Behavior Configuration
Modify these constants in `server.js`:
- `WELCOME_GREETING`: Initial message when call connects
- `SYSTEM_PROMPT`: Instructions for AI assistant behavior
- Model configuration in `aiResponse()` function (currently `gpt-4o-mini`)

## Dependencies

**Core Framework**: Fastify with WebSocket and form body parsing support
**AI Integration**: OpenAI client library
**Configuration**: dotenv for environment variable management

## Development Notes

- **ngrok URL Updates**: Must update NGROK_URL in `.env` and Twilio webhook each time ngrok restarts
- **Session Cleanup**: WebSocket close events properly clean up session storage
- **Voice Optimization**: System prompt includes specific instructions for voice synthesis (spell out numbers, avoid special characters)
- **Real-time Processing**: No streaming implementation - responses sent as complete messages

## Related Branches
The repository includes branches demonstrating advanced features:
- Streaming token responses
- Interruption handling
- Tool/function calling capabilities
