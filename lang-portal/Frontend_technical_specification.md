# Frontend technical spec

## Pages

### Dashboard `/dashboard`
#### Purpose
The purpos of this page is to provide a summary of learning and act as the default page when a user visite the web-app

#### Components 

- Last Stduy Session
    - shows last activity used
    - shows when last activity used
    - summarizes wrong vs correct from last activity
    - has a link to the group

- Study progress
    - total words studied 
        - across all study session show the total word studied out of all possible words in our database
    - display a mastrey progress eg. 0%
- Quick start
    - success reate eg. 80%
    - total study sesssions
    - total active grouos eg. 4 days
- Start Studying Button
    - goes to study ativities page 
We'll need follwing API endponts to power this page 

#### Nedeed API Endpoints
- GET /api/dashboard/last_study_session
- GET /api/dashboard/study_progress
- GET /api/dashboard/quick_stats


### Study Activites `/study-activities`

##### Purpose
The purpose of this page is to show a collection of study activities with a thumbnail and its name, to either launch or view the study activites


#### Components 

- Study Activity Card 
    - show a thumbnail of the study activity
    - then name of the study activity 
    - a launch button to take us to the launch page
    - the view page to view more information about past study
     sessions for this study activity

#### Nedeed API Endpoints
- GET /api/study_activities



### Study Activity show `/study_activities/:id`

##### Purpose
The purpos of this page is to show details of study activity and its past study sessions.

#### Components 
- Name of Study Activity 
- Thumbnail of Study Activity
- Description of Study activity 
- launch button
- Study activity paginated list 
    - id 
    - acivity name 
    - group name 
    - start time  
    - end time (inferred by the last word_review_item submitted )
    - number of review items


#### Nedeed API Endpoints
- GET /api/study_activities/:id
- GET /api/study_activities/:id/study_session


----------


### Study Activity Launch `/study_activities/:id/launch`

##### Purpose
The purpos of this page is to launch a study activity.

#### Components 

- Launch form
    - select field
    - date field for group
    - launch now button
#### Behaviour 

After the form is submitted a new tab opens with the study activity based on its url provided in the database.

Also the after form is submitted the page will redirect to the study session show page 


#### Nedeed API Endpoints
- POST /api/study_activites

### Words `/words`

##### Purpose
The purpos of this page is to show collection of all words in our databse.

#### Components 

- Paginated Word List
        - Columns
        - Japanese
        - Romaji
        - English
        - Correct count 
        - Wrong count 
    - Pagination with 100 items per page
    - Clicking the japanesse word will take us to the word show page

#### Nedeed API Endpoints

- GET /api/words

### Words Show Index `/words/:id`

##### Purpose
The purpos of this page is to show inforamtion about a specific word.
#### Components 

- Japanese
- Romaji
- English
- Study Statictics
    - Correct Count 
    - Wrong Count 
- Word Groups 
    - show an a series of pills eg. tags
    - when group name is clicked it will take us to the group show page 
#### Nedeed API Endpoints

- GET /api/words/:id


### Words Groups  `/groups`

##### Purpose
The purpos of this page is to show a list of groups in our database.
#### Components 

- Paginated Group List
    - Columns
        - Group Name
         
#### Nedeed API Endpoints

- GET /api/groups



### Words Show Index `/groups/:id`

##### Purpose

The purpos of this page is to show a information about specific group.

#### Components 

- Group Name
- Group Statisitcs
    - Total word count 
- Words In Group (Paginateds List of Words)
- Paginated Group List
    - Should use the same component as the words index page
- Study Sessions (Paginated list of study session)
    - Should use the same component as the study sessions index page          

#### Nedeed API Endpoints

- GET /api/groups/:id/words (The name and group stats)
- GET /api/groups/:id/words
- GET /api/groups/:id/study_sessions





### Study Session `/`

##### Purpose

The purpos of this page is to show a information about specific group.

#### Components 

- Group Name
- Group Statisitcs
    - Total word count 
- Words In Group (Paginateds List of Words)
- Paginated Group List
    - Should use the same component as the words index page
- Study Sessions (Paginated list of study session)
    - Should use the same component as the study sessions index page          

#### Nedeed API Endpoints

- GET /api/groups/:id/words (The name and group stats)
- GET /api/groups/:id/words
- GET /api/groups/:id/study_sessions


## #Study Session Index `/study_sessions`


#### Purpose

The purpose of this page is to show a list of study sessions in our database


#### Components 
- Paginated Study Session List 
    - Columns
        - Activity Name 
        - Group Name 
        - Start Time 
        - End Time
        - Number of Review Items
    - Clicking the study session id will take us to the study session show page 
#### Nedeed API Endpoints

- GET /api/study_sessions


### Study Session Show  `/study_sessions/:id`


#### Purpose

The purpose of this page is to show information about a specific study session


#### Components 
- Study Session Details
    - Activity Name 
    - Group Name
    - Start Time
    - End Time 
    - Number of review items
- Words review items (Paginated List of words)
    - Should be the same component as the word index page 

#### Nedeed API Endpoints

- GET /api/study_sessions/:id/words




### Settings Page  `/setting`


#### Purpose

The purpose of this page is to make configurations to the study protal.


#### Components 
- Theme selection eg. Light Dark, System default
- Reset History Button
    - this will delete all study sessions and word review items
- Full reset Button
    - This will drop all tables and re-create with seed data 

#### Nedeed API Endpoints

- POST /api/reset_history
- POST /api/full_reset