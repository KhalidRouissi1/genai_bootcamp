# Technical Specs

## Business Goal: 
- A language learning school wants to build a prototype of learning portal which will act as three things:
- Inventory of possible vocabulary that can be learned
- Act as a  Learning record store (LRS), providing correct and wrong score on practice vocabulary
- A unified launchpad to launch different learning apps


## Technical Requirments
- The backend will be using go
- The database will be SQLite3
- The API will be using gin
- The API will always back JSON


## Database Schema 
We have the following tables:
- words - stored vocabulary words
  - id
  - japasese
  - nomaji
  - english
  - parts

- words_groups - join table for words and groups (many-to-many relationship)
  - id: integer
  - word_id: integer (foreign key to words table)
  - group_id: integer (foreign key to groups table)

- groups - thematic groups of words
  - id: integer
  - name: string

- study_sessions - records of study sessions
  - id: integer
  - group_id: integer (foreign key to groups table)
  - created_at: datetime
  - study_activity_id: integer (foreign key to study_activities table)

- study_activities - a specific study activity, linking a study session to group
  - id: integer
  - study_session_id: integer (foreign key to study_sessions table)
  - group_id: integer (foreign key to groups table)
  - created_at: datetime

- word_review_items - a record of word practice, determining if the word was correct or not
  - word_id: integer (foreign key to words table)
  - study_session_id: integer (foreign key to study_sessions table)
  - correct: boolean
  - created_at: datetime

  ## API Endpoints

  - GET /dashboard/last_study_session
  - GET /dashboard/study_progress
  - GET /dashboard/quick_stats
  - GET /api/study_activities/:id
  - GET /api/study_activities/:id/study_session
  - GET /api/study_activities
  - GET /api/words
  - GET /api/words/:id
  - GET /api/groups
  - GET /api/groups/:id
  - GET /api/groups/:id/words
  - POST /api/study_activites
    - Required params: group_id, study_activity_id
  - GET /api/words
    - Pagination with 100 itmes per page 
  - GET /api/groups
  - GET /api/groups/:id/study_sessions
  - GET /api/study_sessions/:id
  - GET /api/study_sessions/:id/words
  - POST /api/reset_history
  - POST /api/full_reset     
  - POST /api/study_sessions/:id/words/:word_id/review
    - required parmas: Correct 