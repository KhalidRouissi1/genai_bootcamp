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

Our database will be a single sqlite database called words.db
that will be in the root of the project folder of beckend_go

We have the following tables:
- words - stored vocabulary words
  - id
  - japanese
  - romaji
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

### Dashboard Endpoints

#### GET /api/dashboard/last_study_session
**Purpose**: Get information about the user's most recent study session

**Response**:
```json
{
  "id": 123,
  "group_id": 5,
  "activity_name": "Typing Practice",
  "created_at": "2024-01-15T10:30:00Z",
  "correct_count": 15,
  "wrong_count": 3
}
```

**Error Response**:
```json
{
  "error": "No study sessions found"
}
```

#### GET /api/dashboard/study_progress
**Purpose**: Get overall study progress statistics

**Response**:
```json
{
  "total_vocabulary": 1500,
  "total_words_studied": 450,
  "mastered_words": 120,
  "success_rate": 85.5,
  "total_sessions": 25,
  "active_groups": 8,
  "current_streak": 7
}
```

#### GET /api/dashboard/quick_stats
**Purpose**: Get quick statistics for dashboard display

**Response**:
```json
{
  "total_vocabulary": 1500,
  "total_words_studied": 450,
  "mastered_words": 120,
  "success_rate": 85.5,
  "total_sessions": 25,
  "active_groups": 8,
  "current_streak": 7
}
```

### Study Activities Endpoints

#### GET /api/study_activities
**Purpose**: Get list of all available study activities

**Response**:
```json
[
  {
    "id": 1,
    "title": "Typing Practice",
    "launch_url": "http://localhost:3001/typing-tutor",
    "preview_url": "http://localhost:3001/typing-tutor/preview"
  },
  {
    "id": 2,
    "title": "Flashcard Review",
    "launch_url": "http://localhost:3002/flashcards",
    "preview_url": "http://localhost:3002/flashcards/preview"
  }
]
```

#### GET /api/study_activities/:id
**Purpose**: Get details of a specific study activity

**Response**:
```json
{
  "id": 1,
  "title": "Typing Practice",
  "launch_url": "http://localhost:3001/typing-tutor",
  "preview_url": "http://localhost:3001/typing-tutor/preview"
}
```

**Error Response**:
```json
{
  "error": "Study activity not found"
}
```

#### GET /api/study_activities/:id/study_session
**Purpose**: Get study sessions for a specific activity

**Response**:
```json
{
  "sessions": [
    {
      "id": 123,
      "group_id": 5,
      "group_name": "Basic Vocabulary",
      "activity_id": 1,
      "activity_name": "Typing Practice",
      "start_time": "2024-01-15T10:30:00Z",
      "end_time": "2024-01-15T10:45:00Z",
      "review_items_count": 18
    }
  ],
  "total_pages": 3,
  "current_page": 1
}
```

### Words Endpoints

#### GET /api/words
**Purpose**: Get paginated list of words (100 items per page as required by frontend)

**Query Parameters**:
- `page` (optional): Page number (default: 1)
- `sort_by` (optional): Sort field - japanese, romaji, english (default: japanese)
- `order` (optional): Sort order - asc, desc (default: asc)

**Response**:
```json
{
  "words": [
    {
      "id": 1,
      "japanese": "猫",
      "romaji": "neko",
      "english": "cat",
      "correct_count": 5,
      "wrong_count": 2,
      "groups": [
        {
          "id": 1,
          "name": "Animals"
        },
        {
          "id": 3,
          "name": "Basic Vocabulary"
        }
      ]
    }
  ],
  "total_pages": 15,
  "current_page": 1,
  "total_words": 1500
}
```

#### GET /api/words/:id
**Purpose**: Get details of a specific word

**Response**:
```json
{
  "word": {
    "id": 1,
    "japanese": "猫",
    "romaji": "neko",
    "english": "cat",
    "correct_count": 5,
    "wrong_count": 2,
    "groups": [
      {
        "id": 1,
        "name": "Animals"
      },
      {
        "id": 3,
        "name": "Basic Vocabulary"
      }
    ]
  }
}
```

**Error Response**:
```json
{
  "error": "Word not found"
}
```

### Groups Endpoints

#### GET /api/groups
**Purpose**: Get paginated list of word groups

**Query Parameters**:
- `page` (optional): Page number (default: 1)
- `sort_by` (optional): Sort field - name (default: name)
- `order` (optional): Sort order - asc, desc (default: asc)

**Response**:
```json
{
  "groups": [
    {
      "id": 1,
      "group_name": "Animals",
      "word_count": 25
    },
    {
      "id": 2,
      "group_name": "Food",
      "word_count": 30
    }
  ],
  "total_pages": 5,
  "current_page": 1
}
```

#### GET /api/groups/:id
**Purpose**: Get details of a specific group

**Response**:
```json
{
  "id": 1,
  "group_name": "Animals",
  "word_count": 25
}
```

**Error Response**:
```json
{
  "error": "Group not found"
}
```

#### GET /api/groups/:id/words
**Purpose**: Get paginated list of words in a specific group (includes group name and stats as required by frontend)

**Query Parameters**:
- `page` (optional): Page number (default: 1)
- `sort_by` (optional): Sort field - japanese, romaji, english (default: japanese)
- `order` (optional): Sort order - asc, desc (default: asc)

**Response**:
```json
{
  "group": {
    "id": 1,
    "group_name": "Animals",
    "word_count": 25
  },
  "words": [
    {
      "id": 1,
      "japanese": "猫",
      "romaji": "neko",
      "english": "cat",
      "correct_count": 5,
      "wrong_count": 2,
      "groups": [
        {
          "id": 1,
          "name": "Animals"
        }
      ]
    }
  ],
  "total_pages": 3,
  "current_page": 1
}
```

#### GET /api/groups/:id/words/raw
**Purpose**: Get raw list of words in a group (for game integration)

**Response**:
```json
[
  {
    "id": 1,
    "japanese": "猫",
    "romaji": "neko",
    "english": "cat"
  },
  {
    "id": 2,
    "japanese": "犬",
    "romaji": "inu",
    "english": "dog"
  }
]
```

#### GET /api/groups/:id/study_sessions
**Purpose**: Get study sessions for a specific group

**Query Parameters**:
- `page` (optional): Page number (default: 1)

**Response**:
```json
{
  "sessions": [
    {
      "id": 123,
      "group_id": 1,
      "group_name": "Animals",
      "activity_id": 1,
      "activity_name": "Typing Practice",
      "start_time": "2024-01-15T10:30:00Z",
      "end_time": "2024-01-15T10:45:00Z",
      "review_items_count": 18
    }
  ],
  "total_pages": 2,
  "current_page": 1
}
```

### Study Sessions Endpoints

#### GET /api/study_sessions
**Purpose**: Get paginated list of all study sessions

**Query Parameters**:
- `page` (optional): Page number (default: 1)

**Response**:
```json
{
  "sessions": [
    {
      "id": 123,
      "group_id": 1,
      "group_name": "Animals",
      "activity_id": 1,
      "activity_name": "Typing Practice",
      "start_time": "2024-01-15T10:30:00Z",
      "end_time": "2024-01-15T10:45:00Z",
      "review_items_count": 18
    },
    {
      "id": 124,
      "group_id": 2,
      "group_name": "Food",
      "activity_id": 1,
      "activity_name": "Typing Practice",
      "start_time": "2024-01-15T11:00:00Z",
      "end_time": "2024-01-15T11:15:00Z",
      "review_items_count": 22
    }
  ],
  "total_pages": 5,
  "current_page": 1
}
```

#### GET /api/study_sessions/:id
**Purpose**: Get details of a specific study session

**Response**:
```json
{
  "session": {
    "id": 123,
    "group_id": 1,
    "group_name": "Animals",
    "activity_id": 1,
    "activity_name": "Typing Practice",
    "start_time": "2024-01-15T10:30:00Z",
    "end_time": "2024-01-15T10:45:00Z",
    "review_items_count": 18
  },
  "words": [
    {
      "id": 1,
      "japanese": "猫",
      "romaji": "neko",
      "english": "cat",
      "correct_count": 5,
      "wrong_count": 2,
      "groups": [
        {
          "id": 1,
          "name": "Animals"
        }
      ]
    }
  ],
  "total": 18,
  "page": 1,
  "per_page": 50,
  "total_pages": 1
}
```

**Error Response**:
```json
{
  "error": "Study session not found"
}
```

#### GET /api/study_sessions/:id/words
**Purpose**: Get words reviewed in a specific study session

**Query Parameters**:
- `page` (optional): Page number (default: 1)

**Response**:
```json
{
  "words": [
    {
      "id": 1,
      "japanese": "猫",
      "romaji": "neko",
      "english": "cat",
      "correct_count": 5,
      "wrong_count": 2,
      "groups": [
        {
          "id": 1,
          "name": "Animals"
        }
      ]
    }
  ],
  "total": 18,
  "page": 1,
  "per_page": 50,
  "total_pages": 1
}
```

### POST Endpoints

#### POST /api/study_activites
**Purpose**: Create a new study session (called from study activity launch page)

**Request Body**:
```json
{
  "group_id": 1,
  "study_activity_id": 1
}
```

**Response**:
```json
{
  "session_id": 124,
  "group_id": 1,
  "activity_id": 1,
  "created_at": "2024-01-15T11:00:00Z"
}
```

**Error Response**:
```json
{
  "error": "Invalid group_id or study_activity_id"
}
```

**Note**: After this endpoint is called, the frontend will:
1. Open a new tab with the study activity URL
2. Redirect to the study session show page (`/study_sessions/:session_id`)

#### POST /api/study_sessions/:id/words/:word_id/review
**Purpose**: Submit a word review (correct/incorrect)

**Request Body**:
```json
{
  "correct": true
}
```

**Response**:
```json
{
  "success": true,
  "word_id": 1,
  "session_id": 123,
  "correct": true,
  "created_at": "2024-01-15T11:05:00Z"
}
```

**Error Response**:
```json
{
  "error": "Invalid session or word"
}
```

#### POST /api/reset_history
**Purpose**: Reset all study history (sessions and reviews)

**Response**:
```json
{
  "success": true,
  "message": "Study history has been reset"
}
```

#### POST /api/full_reset
**Purpose**: Full database reset with seed data

**Response**:
```json
{
  "success": true,
  "message": "Database has been fully reset with seed data"
}
```

## Common Response Patterns

### Pagination
Most list endpoints follow this pagination pattern:
```json
{
  "data": [...],
  "total_pages": 5,
  "current_page": 1,
  "total_items": 125
}
```

### Error Responses
All endpoints return errors in this format:
```json
{
  "error": "Error message description"
}
```

Common HTTP status codes:
- `200` - Success
- `404` - Resource not found
- `400` - Bad request (invalid parameters)
- `500` - Internal server error

### Date Format
All dates are returned in ISO 8601 format:
```
"2024-01-15T10:30:00Z"
```

### Word Object Structure
The standard word object includes:
```json
{
  "id": 1,
  "japanese": "猫",
  "romaji": "neko",
  "english": "cat",
  "correct_count": 5,
  "wrong_count": 2,
  "groups": [
    {
      "id": 1,
      "name": "Animals"
    }
  ]
}
```

### Group Object Structure
The standard group object includes:
```json
{
  "id": 1,
  "group_name": "Animals",
  "word_count": 25
}
```

### Study Session Object Structure
The standard study session object includes:
```json
{
  "id": 123,
  "group_id": 1,
  "group_name": "Animals",
  "activity_id": 1,
  "activity_name": "Typing Practice",
  "start_time": "2024-01-15T10:30:00Z",
  "end_time": "2024-01-15T10:45:00Z",
  "review_items_count": 18
}
```

## Frontend Page to API Endpoint Mapping

This section maps each frontend page to its required API endpoints as specified in the Frontend Technical Specification:

### Dashboard (`/dashboard`)
- GET /api/dashboard/last_study_session
- GET /api/dashboard/study_progress
- GET /api/dashboard/quick_stats

### Study Activities (`/study-activities`)
- GET /api/study_activities

### Study Activity Show (`/study_activities/:id`)
- GET /api/study_activities/:id
- GET /api/study_activities/:id/study_session

### Study Activity Launch (`/study_activities/:id/launch`)
- POST /api/study_activites

### Words Index (`/words`)
- GET /api/words (100 items per page)

### Word Show (`/words/:id`)
- GET /api/words/:id

### Groups Index (`/groups`)
- GET /api/groups

### Group Show (`/groups/:id`)
- GET /api/groups/:id/words (includes group name and stats)
- GET /api/groups/:id/study_sessions

### Study Sessions Index (`/study_sessions`)
- GET /api/study_sessions

### Study Session Show (`/study_sessions/:id`)
- GET /api/study_sessions/:id/words

### Settings (`/settings`)
- POST /api/reset_history
- POST /api/full_reset

## Additional Endpoints

### Game Integration
- GET /api/groups/:id/words/raw (for typing tutor and other games)

### Review Submission
- POST /api/study_sessions/:id/words/:word_id/review (called by games to submit word reviews)

## Mage {Tasks}
Mage is a task runnder for go
Lets list out possible tasks we need for our lang protal

### Initaialize Database

This task will initailize the sqlite db called words.db

### Migration Database
This task will run a series of migrations sql files on the database
Migrations will live in `migration` folder.
The migration files will be run in order of thier file name.
The file names should look like this :
```sql
0001_init.sql
0002_create_words_table.sql
```
### Seed Data
This data will import json files and transfore them into targate data for our database.
All seed files live in the `seeds` folder 
All seed files should be loaded.

In our task we should have a DSL to each seed file and its expected group word name.
```json
 {
    "kanji": "いい",
    "romaji": "ii",
    "english": "good",
    "parts": [
      { "kanji": "い", "romaji": ["i"] },
      { "kanji": "い", "romaji": ["i"] }
    ]
  }
```