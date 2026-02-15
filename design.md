# Yatharth.AI - Design Document

## System Architecture

### High-Level Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Web App    │  │  Mobile App  │  │  Voice Bot   │      │
│  │  (Amplify)   │  │ (React Native)│  │  (Alexa)    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      API Gateway Layer                       │
│              (Amazon API Gateway + WAF)                      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                   Orchestration Layer                        │
│                     (AWS Lambda)                             │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐      │
│  │  Voice   │ │  Career  │ │  Resume  │ │ Resource │      │
│  │ Handler  │ │ Advisor  │ │ Builder  │ │ Matcher  │      │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      AI Services Layer                       │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Bedrock    │  │  Transcribe  │  │    Polly     │      │
│  │ (Claude 3)   │  │   (Speech)   │  │   (Voice)    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Translate   │  │   Textract   │  │ Comprehend   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                       Data Layer                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  DynamoDB    │  │  OpenSearch  │  │      S3      │      │
│  │ (User Data)  │  │   (Vector)   │  │   (Assets)   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

## Component Design

### 1. Vernacular Voice Companion

#### Architecture Flow
```
User Speech → Transcribe → Translate → Bedrock → Translate → Polly → Audio Response
```

#### Components
**A. Voice Input Handler**
- Service: AWS Transcribe
- Languages: hi-IN, ta-IN, te-IN, en-IN
- Format: WebM/Opus for web, AAC for mobile
- Streaming: Real-time transcription for low latency

**B. Language Router**
- Detects source language from transcription
- Routes to appropriate translation pipeline
- Maintains language preference in session

**C. Context Manager**
- Stores conversation history in DynamoDB
- Implements sliding window (last 10 exchanges)
- Injects context into Bedrock prompts

**D. LLM Orchestrator**
- Model: Claude 3 Sonnet (Bedrock)
- Temperature: 0.7 for conversational tone
- Max tokens: 500 for concise responses
- System prompt: Career guidance expert persona

**E. Voice Output Generator**
- Service: Amazon Polly
- Voices: Aditi (Hindi), Kajal (Hinglish), custom neural voices
- SSML tags for natural pauses and emphasis

#### Data Models
```json
{
  "conversationId": "uuid",
  "userId": "string",
  "timestamp": "ISO8601",
  "language": "hi-IN",
  "userInput": {
    "audio": "s3://bucket/audio.webm",
    "transcript": "मुझे AI में करियर चाहिए"
  },
  "aiResponse": {
    "text": "AI में करियर के लिए...",
    "audio": "s3://bucket/response.mp3"
  },
  "context": ["previous", "messages"]
}
```

### 2. Parent Consensus Engine

#### Architecture
```
Career Input → Career DB Lookup → Analogy Generator → Cultural Adapter → Audio Generator
```

#### Components
**A. Career Knowledge Base**
- Storage: OpenSearch Serverless
- Schema: Career title, description, salary, skills, growth
- Embeddings: Titan Embeddings for semantic search

**B. Analogy Generator**
- Prompt engineering for cultural translation
- Template: "Like a [traditional job], but [modern aspect]"
- Includes: Stability score, salary comparison, respect factor

**C. Regional Customizer**
- Adapts language based on user's state
- Uses regional job examples (e.g., "like a PSU job in Gujarat")
- Cultural sensitivity filters

**D. Multi-Audience Renderer**
- Generates two versions: student-facing and parent-facing
- Parent version: Emphasizes stability, respect, salary
- Student version: Emphasizes growth, learning, innovation

#### Data Models
```json
{
  "careerId": "prompt-engineer",
  "modernTitle": "Prompt Engineer",
  "parentAnalogy": "Like a lawyer drafting precise documents",
  "salaryRange": "8-15 LPA",
  "comparableJobs": ["Bank PO", "Government Officer"],
  "stabilityScore": 8.5,
  "growthProjection": "25% YoY",
  "regionalContext": {
    "north": "Similar to IAS preparation coaching",
    "south": "Like software engineer but specialized"
  }
}
```

### 3. Plan B Generator

#### Architecture
```
Primary Goal → Syllabus Vectorization → Similarity Search → Ranking → Roadmap Generation
```

#### Components
**A. Exam Syllabus Database**
- Storage: S3 (raw PDFs) + OpenSearch (vectors)
- Coverage: UPSC, JEE, NEET, CAT, GATE, State PSCs
- Update frequency: Quarterly

**B. Vector Similarity Engine**
- Embeddings: AWS Bedrock Titan Embeddings
- Similarity metric: Cosine similarity
- Threshold: > 40% overlap for recommendations

**C. Backup Ranker**
- Factors: Syllabus overlap, difficulty, competition, ROI
- Weighted scoring algorithm
- Personalization based on user strengths

**D. Timeline Generator**
- Calculates preparation time based on overlap
- Considers user's current preparation stage
- Generates week-by-week study plan

#### Data Models
```json
{
  "primaryGoal": "UPSC CSE",
  "backupOptions": [
    {
      "exam": "State PSC",
      "overlapPercentage": 75,
      "additionalTopics": ["State History", "Local Governance"],
      "preparationTime": "3 months",
      "successRate": "12%",
      "avgSalary": "6-8 LPA"
    }
  ],
  "riskScore": "High",
  "recommendation": "Start State PSC prep alongside UPSC"
}
```

### 4. Voice-to-Resume Builder

#### Architecture
```
Voice Input → Entity Extraction → Resume Structuring → Template Application → PDF Generation
```

#### Components
**A. Information Extractor**
- Service: Bedrock with custom prompt
- Entities: Name, education, skills, experience, projects
- Validation: Checks for completeness

**B. Resume Structurer**
- Converts unstructured data to ATS-friendly format
- Bullet point generation for experience
- Action verb optimization

**C. Template Engine**
- Templates: Harvard, Modern, Technical, Creative
- LaTeX-based for professional formatting
- Customizable sections

**D. PDF Generator**
- Service: AWS Lambda with Puppeteer/WeasyPrint
- Output: A4 size, ATS-compatible
- Storage: S3 with signed URLs

#### Data Models
```json
{
  "resumeId": "uuid",
  "userId": "string",
  "sections": {
    "personal": {
      "name": "string",
      "email": "string",
      "phone": "string",
      "location": "string"
    },
    "education": [
      {
        "degree": "B.Tech",
        "institution": "string",
        "year": "2024",
        "cgpa": "8.5"
      }
    ],
    "skills": ["Python", "AWS", "React"],
    "experience": [
      {
        "title": "Intern",
        "company": "string",
        "duration": "3 months",
        "bullets": ["Achieved X", "Built Y"]
      }
    ]
  },
  "template": "harvard",
  "pdfUrl": "s3://bucket/resume.pdf"
}
```

### 5. False Mastery Detector

#### Architecture
```
Skill Claim → Question Bank Lookup → Adaptive Quiz → Performance Analysis → Proficiency Score
```

#### Components
**A. Question Bank**
- Storage: DynamoDB with GSI on skill
- Difficulty levels: Easy, Medium, Hard, Expert
- Question types: MCQ, code snippets, scenario-based

**B. Adaptive Quiz Engine**
- Algorithm: Item Response Theory (IRT)
- Adjusts difficulty based on previous answers
- Minimum 5 questions, maximum 10

**C. Performance Analyzer**
- Calculates proficiency score (0-100)
- Maps to levels: Beginner (0-40), Intermediate (41-70), Advanced (71-90), Expert (91-100)
- Identifies weak areas

**D. Learning Path Generator**
- Recommends resources based on gaps
- Integrates with free learning platforms
- Tracks improvement over time

#### Data Models
```json
{
  "assessmentId": "uuid",
  "userId": "string",
  "skill": "Java",
  "claimedLevel": "Expert",
  "questions": [
    {
      "id": "q123",
      "difficulty": "hard",
      "answer": "B",
      "correct": true,
      "timeTaken": 45
    }
  ],
  "actualLevel": "Intermediate",
  "proficiencyScore": 68,
  "weakAreas": ["Concurrency", "JVM Internals"],
  "recommendations": ["Course links", "Practice problems"]
}
```

### 6. AI Soft-Skill Dojo

#### Architecture
```
Scenario Selection → Voice Recording → Analysis (Pace, Tone, Content) → Feedback Generation
```

#### Components
**A. Scenario Library**
- Categories: Interview, Negotiation, Presentation, Conflict
- Industry-specific scenarios
- Difficulty progression

**B. Voice Analyzer**
- Service: AWS Transcribe + Comprehend
- Metrics: WPM, filler words, sentiment, confidence
- Prosody analysis for tone

**C. Content Evaluator**
- Service: Bedrock
- Checks: Clarity, structure, persuasiveness
- Compares against best practices

**D. Feedback Generator**
- Real-time suggestions during practice
- Post-session detailed report
- Progress tracking dashboard

#### Data Models
```json
{
  "sessionId": "uuid",
  "userId": "string",
  "scenario": "Technical Interview",
  "recording": "s3://bucket/session.mp3",
  "analysis": {
    "pace": 145,
    "fillerWords": 12,
    "confidenceScore": 7.5,
    "clarity": 8.0,
    "structure": 6.5
  },
  "feedback": [
    "Reduce 'um' and 'uh' usage",
    "Speak 10% slower for clarity",
    "Strong opening, weak conclusion"
  ],
  "improvementTrend": "+15% vs last session"
}
```

### 7. Scholarship Matchmaker

#### Architecture
```
User Profile → Eligibility Checker → Probability Calculator → Ranking → Application Assistant
```

#### Components
**A. Scholarship Database**
- Source: Government websites, web scraping
- Update: Weekly automated refresh
- Storage: DynamoDB with GSI on eligibility criteria

**B. Eligibility Engine**
- Rule-based matching (caste, income, region, education)
- Fuzzy matching for partial eligibility
- Probability scoring (0-100%)

**C. Application Assistant**
- Pre-fills forms with user data
- Generates required documents
- Deadline tracking with reminders

**D. Notification Service**
- Service: Amazon SNS
- Channels: SMS, Email, Push
- Triggers: New scholarships, deadlines, status updates

#### Data Models
```json
{
  "scholarshipId": "uuid",
  "name": "Post Matric Scholarship",
  "provider": "Ministry of Social Justice",
  "amount": "50000 INR/year",
  "eligibility": {
    "caste": ["SC", "ST"],
    "income": "< 2.5 LPA",
    "education": "Class 11+",
    "state": ["All"]
  },
  "deadline": "2026-03-31",
  "applicationUrl": "https://...",
  "requiredDocs": ["Income Certificate", "Caste Certificate"]
}
```

### 8. Roadmap-to-Calendar Sync

#### Architecture
```
Career Goal → Roadmap Generation → Task Breakdown → Calendar Integration → Progress Tracking
```

#### Components
**A. Roadmap Generator**
- Service: Bedrock with career knowledge base
- Generates: Milestones, skills, resources, timeline
- Personalized based on user background

**B. Task Decomposer**
- Breaks milestones into daily/weekly tasks
- Estimates time per task
- Considers user availability

**C. Calendar Integrator**
- API: Google Calendar API
- Creates events with descriptions and resources
- Sets reminders

**D. Progress Tracker**
- Monitors task completion
- Adjusts timeline dynamically
- Sends motivational messages

#### Data Models
```json
{
  "roadmapId": "uuid",
  "userId": "string",
  "goal": "Full Stack Developer",
  "duration": "6 months",
  "milestones": [
    {
      "name": "Learn HTML/CSS",
      "tasks": [
        {
          "title": "Complete HTML basics",
          "duration": "2 hours",
          "resources": ["FreeCodeCamp link"],
          "calendarEventId": "google-event-id",
          "completed": false
        }
      ]
    }
  ],
  "progress": 25,
  "adjustments": ["Extended JS learning by 1 week"]
}
```

## Database Schema

### DynamoDB Tables

**Users Table**
- PK: userId
- Attributes: name, phone, email, language, region, demographics
- GSI: phone-index, email-index

**Conversations Table**
- PK: conversationId
- SK: timestamp
- Attributes: userId, messages, language
- GSI: userId-index

**Assessments Table**
- PK: assessmentId
- Attributes: userId, skill, score, timestamp
- GSI: userId-skill-index

**Roadmaps Table**
- PK: roadmapId
- Attributes: userId, goal, milestones, progress
- GSI: userId-index

### OpenSearch Collections

**Careers Collection**
- Fields: title, description, salary, skills, embedding
- Index: Vector similarity search

**Exams Collection**
- Fields: examName, syllabus, topics, embedding
- Index: Vector similarity search

**Scholarships Collection**
- Fields: name, eligibility, amount, deadline
- Index: Keyword + filter search

## Security Design

### Authentication & Authorization
- Service: Amazon Cognito
- Flow: OAuth 2.0 with JWT tokens
- MFA: SMS-based for sensitive operations
- Session: 7-day refresh token

### Data Protection
- Encryption at rest: AWS KMS
- Encryption in transit: TLS 1.3
- Audio data: Deleted after processing
- PII: Encrypted in DynamoDB

### API Security
- AWS WAF: Rate limiting, SQL injection protection
- API Gateway: Throttling (1000 req/min per user)
- Lambda: IAM roles with least privilege

## Monitoring & Observability

### Metrics
- Service: Amazon CloudWatch
- Key metrics: Latency, error rate, user engagement
- Alarms: > 5% error rate, > 3s latency

### Logging
- Service: CloudWatch Logs
- Retention: 30 days
- Log levels: ERROR, WARN, INFO

### Tracing
- Service: AWS X-Ray
- Traces: End-to-end request flow
- Analysis: Bottleneck identification

## Deployment Strategy

### CI/CD Pipeline
- Source: GitHub
- Build: AWS CodeBuild
- Deploy: AWS CodePipeline
- Stages: Dev → Staging → Production

### Infrastructure as Code
- Tool: AWS CDK (TypeScript)
- Stacks: Network, Compute, Data, AI
- Version control: Git

### Rollback Strategy
- Blue-Green deployment for Lambda
- Canary releases for critical features
- Automated rollback on error threshold

## Cost Optimization

### Estimated Monthly Cost (10K users)
- Lambda: $50 (1M invocations)
- Bedrock: $200 (Claude 3 Sonnet)
- Transcribe/Polly: $150
- DynamoDB: $25 (on-demand)
- OpenSearch: $100 (serverless)
- S3: $10
- Total: ~$535/month

### Optimization Strategies
- Lambda: Right-size memory, use ARM architecture
- Bedrock: Cache common responses
- S3: Lifecycle policies for old audio
- DynamoDB: Use on-demand for variable traffic

## Scalability Considerations

### Horizontal Scaling
- Lambda: Auto-scales to 1000 concurrent executions
- API Gateway: Handles 10K RPS
- DynamoDB: Auto-scaling enabled

### Caching Strategy
- API Gateway: Cache common queries (5 min TTL)
- CloudFront: Cache static assets
- Application: Redis for session data (optional)

### Performance Optimization
- Bedrock: Streaming responses for faster TTFB
- Transcribe: Streaming for real-time feedback
- Lambda: Provisioned concurrency for critical functions
