# Yatharth.AI - Requirements Document

## Project Overview
**Yatharth.AI** is a vernacular, voice-first career guidance ecosystem designed to bridge the "Guidance Divide" for Tier-2/3 communities in India. The platform acts as a Family Mediator and Risk Manager, using AWS Generative AI to democratize access to career opportunities.

## Problem Statement
Three critical barriers prevent underserved communities from accessing career opportunities:
1. **Language Barrier**: English-first platforms exclude vernacular-speaking populations
2. **Family Barrier**: Inability to convince risk-averse parents about modern career paths
3. **Fear Barrier**: Lack of backup options creates exam-related anxiety

## Target Users
- **Primary**: Students from Tier-2/3 cities (ages 16-25)
- **Secondary**: Parents and guardians making collective career decisions
- **Tertiary**: Rural talent with limited English proficiency

## Functional Requirements

### Module A: Community Access (Guidance & Safety)

#### FR-A1: Vernacular Voice Companion
- **FR-A1.1**: Support speech input in Hindi, Hinglish, Tamil, and Telugu
- **FR-A1.2**: Convert speech to text with 90%+ accuracy
- **FR-A1.3**: Process natural language queries about careers, education, and opportunities
- **FR-A1.4**: Generate contextually relevant responses using LLM
- **FR-A1.5**: Convert text responses to natural-sounding speech in user's language
- **FR-A1.6**: Maintain conversation context across multiple interactions
- **FR-A1.7**: Support low-bandwidth audio streaming (< 50 kbps)

#### FR-A2: Parent Consensus Engine
- **FR-A2.1**: Accept modern career titles as input (e.g., "Prompt Engineer", "UX Designer")
- **FR-A2.2**: Generate culturally relatable analogies for Indian parents
- **FR-A2.3**: Include salary ranges in INR with comparable traditional jobs
- **FR-A2.4**: Provide job stability metrics and growth projections
- **FR-A2.5**: Generate separate audio explanations for parents vs students
- **FR-A2.6**: Support regional cultural contexts (North/South/East/West India)
- **FR-A2.7**: Create shareable audio clips for family discussions

#### FR-A3: Plan B Generator
- **FR-A3.1**: Accept primary career goal or exam target (UPSC, JEE, NEET, etc.)
- **FR-A3.2**: Analyze syllabus overlap using vector similarity search
- **FR-A3.3**: Generate ranked list of backup options (minimum 5 alternatives)
- **FR-A3.4**: Show percentage overlap with primary goal
- **FR-A3.5**: Provide timeline and effort estimates for each backup
- **FR-A3.6**: Include success rates and competition levels
- **FR-A3.7**: Update recommendations based on preparation progress

### Module B: Real-World Employability (Validation)

#### FR-B1: Voice-to-Resume Builder
- **FR-B1.1**: Accept voice input in vernacular languages
- **FR-B1.2**: Extract structured information (education, skills, experience)
- **FR-B1.3**: Generate professional English resume in ATS-friendly format
- **FR-B1.4**: Support multiple resume templates (Harvard, Modern, Technical)
- **FR-B1.5**: Export to PDF with proper formatting
- **FR-B1.6**: Allow iterative refinement through voice commands
- **FR-B1.7**: Validate completeness and suggest improvements

#### FR-B2: False Mastery Detector
- **FR-B2.1**: Accept skill claims from user profile
- **FR-B2.2**: Generate adaptive micro-quizzes (5-10 questions)
- **FR-B2.3**: Adjust difficulty based on previous answers
- **FR-B2.4**: Provide proficiency score (Beginner/Intermediate/Advanced/Expert)
- **FR-B2.5**: Identify knowledge gaps with learning resources
- **FR-B2.6**: Support technical and soft skills assessment
- **FR-B2.7**: Generate skill verification certificates

#### FR-B3: AI Soft-Skill Dojo
- **FR-B3.1**: Provide roleplay scenarios (interviews, negotiations, presentations)
- **FR-B3.2**: Analyze voice for confidence, pace, and clarity
- **FR-B3.3**: Detect filler words and speech patterns
- **FR-B3.4**: Provide real-time feedback during practice
- **FR-B3.5**: Track improvement over time with metrics
- **FR-B3.6**: Support industry-specific scenarios
- **FR-B3.7**: Generate practice reports with actionable insights

### Module C: Public Resources (Support)

#### FR-C1: Scholarship Matchmaker
- **FR-C1.1**: Collect user demographics (caste, income, region, education)
- **FR-C1.2**: Match against government scholarship database
- **FR-C1.3**: Calculate eligibility probability (0-100%)
- **FR-C1.4**: Rank scholarships by amount and probability
- **FR-C1.5**: Provide application deadlines and requirements
- **FR-C1.6**: Generate pre-filled application drafts
- **FR-C1.7**: Send deadline reminders via notifications

#### FR-C2: Roadmap-to-Calendar Sync
- **FR-C2.1**: Generate personalized career roadmaps
- **FR-C2.2**: Break roadmaps into daily/weekly tasks
- **FR-C2.3**: Integrate with Google Calendar API
- **FR-C2.4**: Set realistic deadlines based on user availability
- **FR-C2.5**: Include learning resources for each task
- **FR-C2.6**: Track completion and adjust timeline dynamically
- **FR-C2.7**: Send progress notifications and motivational messages

## Non-Functional Requirements

### NFR-1: Performance
- **NFR-1.1**: Voice response latency < 3 seconds
- **NFR-1.2**: Support 10,000+ concurrent users
- **NFR-1.3**: API response time < 500ms (p95)
- **NFR-1.4**: Resume generation < 10 seconds
- **NFR-1.5**: Scholarship matching < 2 seconds

### NFR-2: Scalability
- **NFR-2.1**: Serverless architecture for auto-scaling
- **NFR-2.2**: Handle 100,000+ users within 6 months
- **NFR-2.3**: Support addition of new languages without code changes
- **NFR-2.4**: Database design for 1M+ user profiles

### NFR-3: Accessibility
- **NFR-3.1**: Work on 2G/3G networks (< 100 kbps)
- **NFR-3.2**: Mobile-first responsive design
- **NFR-3.3**: Support low-end Android devices (Android 8+)
- **NFR-3.4**: Offline mode for core features
- **NFR-3.5**: Screen reader compatibility

### NFR-4: Security & Privacy
- **NFR-4.1**: End-to-end encryption for voice data
- **NFR-4.2**: GDPR and Indian data protection compliance
- **NFR-4.3**: No storage of raw audio beyond processing
- **NFR-4.4**: User consent for data collection
- **NFR-4.5**: Secure authentication (OAuth 2.0)

### NFR-5: Reliability
- **NFR-5.1**: 99.9% uptime SLA
- **NFR-5.2**: Graceful degradation when services fail
- **NFR-5.3**: Automated backup and disaster recovery
- **NFR-5.4**: Error logging and monitoring

### NFR-6: Usability
- **NFR-6.1**: Zero learning curve for voice interface
- **NFR-6.2**: Maximum 3 taps to reach any feature
- **NFR-6.3**: Visual feedback for all voice interactions
- **NFR-6.4**: Support for users with disabilities

## Technical Constraints
- Must use AWS services for core infrastructure
- Must support vernacular languages (Hindi, Tamil, Telugu minimum)
- Must work on low-bandwidth connections
- Must be cost-effective for free tier users
- Must comply with Indian government data regulations

## Success Metrics
- **Adoption**: 50,000+ users in first 6 months
- **Engagement**: 60%+ weekly active users
- **Impact**: 30%+ users report family approval for career choice
- **Validation**: 80%+ accuracy in skill assessment
- **Conversion**: 20%+ users apply to matched scholarships
- **Satisfaction**: 4.5+ star rating on app stores

## Out of Scope (Phase 1)
- Job placement services
- Direct employer connections
- Paid premium features
- International markets
- Video-based content
- Peer-to-peer mentoring

## Dependencies
- AWS Account with Bedrock access
- Google Calendar API access
- Government scholarship database (public APIs or web scraping)
- Exam syllabus data (UPSC, JEE, NEET, etc.)
- Career salary data (India-specific)

## Assumptions
- Users have smartphone access (Android/iOS)
- Users have basic digital literacy
- Internet connectivity available (even if slow)
- Government scholarship data is publicly accessible
- AWS Bedrock supports required languages
