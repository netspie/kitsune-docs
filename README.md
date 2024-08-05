# Kitsune Study Documentation

&nbsp;&nbsp;&nbsp;&nbsp; Japanese language learning app where you can add and explore various materials and turn anything into flashcards.

## General

&nbsp;&nbsp;&nbsp;&nbsp; The main repository is available at https://github.com/netspie/kitsune  

#### Technologies
&nbsp;&nbsp;&nbsp;&nbsp; Asp.Net Core, C#, HTML, CSS, Blazor Server and MudBlazor, MongoDb, AWS, Docker, NGinx

#### Demo Urls
&nbsp;&nbsp;&nbsp;&nbsp; https://kitsunestudy.net (not available right now)

#### Demo Gif

![Demo Image](/images/KS.gif)

#### Features

*Current State*

- Content Creation - Courses, Conversations and Phrases - add, edit and explore learning content
- Flashcard List Generation - Phrases - turn any lesson or conversation phrases into a flashcard list in any mode - reading, listening, speaking
- Progress Tracking - view basic statistics of learned/rehearsed material
- Spaced Repetition Flashcard Generation - Rehearse learned phrases in spaced repetition mode with basic filtering options

*Planned*

- Various Content Creation - informations and questions, kana, kanji, vocabulary, various media - anime, manga, songs, etc.
- Categorization - by theme, linguistics, situation, formality, dialects, jargons
- Flashcard List Generation - kana, kanji, words and its inflections
- Multiselection - select desired content items and add for rehearse or generate flashcards instantly
- Allow any user for adding content by giving a proper role and allow for submitting it for global publish
- Rehearse settings - customize schedule, repetition day intervals, max rehearse items per day + max new items to learn per day
- Subscription Payments - allow user to pay and subscribe for additional benefits, access to all content and features
- Global search
- User settings - change password, username etc.

#### Deliverables

| Current State | Planned |
|----------|----------|
| Single web server app with ui server side ui rendering deployed | Mobile application for android, later for iOS  |

#### Quality Attributes

*Current State*

Since the application is just a prototype and feature development time is a priority, no serious measurements has been performed yet.

*Planned*

- monitoring and observability - logging, real-time user activity
- maintainability - unit tests, E2E tests
- performance - stress, load, endurance tests, etc.
- api and offline access

#### Content

| Current State | Planned |
|----------|----------|
| Test Lesson and Phrase Content | Custom Content Created in collaboration with language experts |
|  | Various media like anime or manga subtitles, songs lyrics and other possible to be added by anyone |

#### Purpose

Address following problems with learning japanese language:  
  
- Writing notes from videos, courses, articles, lectures - which takes effort and time.  
- Material fatigue - lack of innovation of learning process and revisions, which leads to procrastination.  
- Lack of tools, ways for learning material from different angles, ex. reading, listening, speaking, writing.  
- Learning materials scattered all over the internet, instead of in the single place.  
- Hard to get back and rehearse your knowledge in an easy way. No automation. No challenging tasks.  
- If there are high quality materials, they are costly.  

## Architecture

### System Context Diagram
![System Context Diagram](/images/System%20Context%20Diagram.png)

## Runtime Flows

### Add Learning Entity for Rehearse
&nbsp;&nbsp;&nbsp;&nbsp; If a user wants to add whole lesson for rehearse (could be also conversation, song lyrics, anime episode subtitles), one must click a button. After that, a command is send and processed by a command handler, which only thing it does is storing event to the event store for further processing. It is because a collection, here the lesson, may consist of tens or more granular items (concepts, words, phrases, etc.) all nested several levels down, and since it is unknown how many it is, it would be too much work to search everything at once for each single user request performed. The solution here is just a simple outbox pattern - the rehearse module background thread worker collects the first x events in the queue, which is stored in MongoDb and indexed by timestamp ticks and then executes proper event handlers. The similar solution must be done for other operations - like removing collection from rehearse, or especially if adding new or removing nested granular item (phrase, word) or collection when multiple users already have a parent collection added for rehearse, so there is plenty items to process.  

&nbsp;&nbsp;&nbsp;&nbsp; Additionally, for single learning entity (collection or item) there is multiple rehearse data items added for each mode (reading, listening, speaking, writing) depending on the item type and since each granular item can also be a collection of more granular items itself - there is really a lot of data, all of it is required, so the user can easily navigate between various views and data back and forth and review the material in a flexible way.  

&nbsp;&nbsp;&nbsp;&nbsp; For each "Add to Rehearse" event, the handler takes the learning item, which id is present in there and just for a part of its childrens it creates required rehearse data items later used for rehearsing in spaced repetition mode or different ways depending on user's desire. For nested items it creates and store separate events to process it later, so not to perform all the work at once.  

![Add to Rehearse Flow Diagram](/images/Add%20to%20Rehearse%20Flow%20Diagram.png)

## Project Structure

### Solution Structure
![Solution Structure Image](/images/project-structure.png)

### Entities Project
![Entities Project Image](/images/project-entities.png)

### Usecases Project - Content Context
![Project Usecases Content Image](/images/project-usecases-content.png)

### UI Project
![UI Project Image](/images/project-ui.png)

## Environment

### Development
- IDEs - Visual Studio 2022, Notepad++
- Task Management - Trello
- Source Version Control - Git, Github, SourceTree
- MongoDb Database Tools - MongoDb Cloud, MongoDb Shell, Studio 3T, MongoDb Compass
- Design Tools - Miro

#### Setup

- Install latest version of Visual Studio 2022 Community - https://visualstudio.microsoft.com/pl/vs/
- Clone 3rd party and main repositories, example:
```
D:
mkdir git
cd git
git clone https://github.com/drlsn/Kitsune-Study/Corelibs.Basic.git  
git clone https://github.com/drlsn/Kitsune-Study/Corelibs.MongoDB.git  
git clone https://github.com/drlsn/Kitsune-Study/kitsune.git  
```
- Install Git Source Version Control GUI if desired, i.e. SourceTree - https://www.sourcetreeapp.com/
- Install MongoDb Server with Compass - https://www.mongodb.com/try/download/community
- Install MongoDb Shell - https://www.mongodb.com/try/download/shell - make sure you can run command mongosh in cmd line, if not add the path to environment Path variable
- Install MongoDb Command Line Database Tools and set path as environment variable - https://www.mongodb.com/try/download/database-tools
  - Path - C:\Program Files\MongoDB\Tools\bin
- Download desired backup version from a link (secret)
- Run command to restore mongo database and media files - modify if needed, [docs](https://www.mongodb.com/docs/database-tools/mongorestore/#synopsis)
```
mongosh --eval "use Kitsune_dev" --eval "db.dropDatabase()"
mongorestore --uri="mongodb://localhost:27017/Kitsune_dev" D:/kitsune-dumps/15-09-23-01/Kitsune_dev
rmdir /s /q "D:\git\kitsune\src\Manabu.UI.Server\media"
xcopy /s /i /Y "D:\kitsune-dumps\15-09-23-01\media" "D:\git\kitsune\src\Manabu.UI.Server\media"
```
- To enable sorting and filtering of certain data it is needed to create search index - only available in Mongo Atlas
  - words - searchWordsByValue - `{ "mappings": { "fields": { "Value": { "analyzer": "lucene.keyword", "type": "string" } } } }`
  - wordMeanings - searchWordMeanings - `{ "mappings": { "fields": { "Original": { "analyzer": "lucene.keyword", "type": "string" }, "Translations": { "analyzer": "lucene.keyword", "type": "string" } } } }`
- To run server on local network (optional)
  - in D:\git\kitsune\src\Manabu.UI.Server\Properties\launchSettings.json in section profiles/https/applicationUrl add local ip address - don't ever commit
  - open port, i.e. 7073 - https://www.partitionwizard.com/partitionmanager/how-to-open-ports.html
- Set environment variables (optional)
  - Kitsune_UseLocalIP - true - allows to run app on local network so you can run it on a phone
  - KitsuneDatabaseConn - mongodb://localhost:27017
    
### Production
- Domain Provider - [seohost.pl](https://seohost.pl/)
- Backend Host - AWS E2C
- Backend System - Ubuntu
- SSL Certificate - Let's Encrypt, NGinx for https redirection
- Plain Text Editors - Vim for config files editing like nginx.conf
- Build and Deployment - Docker build/push, Python build script for .dockerignore and 3rdparty local project dependendencies generation
- Authentication Service - Azure B2C
- Databases - MongoDb Cloud - data backups done by mongodump and mongorestore
- Static/media files storage - currently local storage with plans to switch to Amazon S3 - media backups done by ssh scp command
- Mailing Newsletter - MailerLite (planned to enable)

## Design

#### User Scenarios

##### Case 1 - Learn the language from scratch in an organized way

*Steps*
- Go to courses page and choose course of appropriate difficulty level
- Read, listen, learn from a lesson, mark it for further rehearse
- Rehearse learned material based on spaced repetition algorithm or customized

##### Case 2 - Learn specific piece of language (grammar point, writing script characters, words)

*Steps*
- Find a specific grammar point, word, or phrase and learn it
- Mark it to remember for rehearse
- Rehearse learned material based on spaced repetition algorithm or customized

##### Case 3 - Learn specific piece of media (movie subtitles, song lyrics, situational phrases?)

*Steps*
- similar as before

##### Case 4 - Learn vocabulary or phrases related to specific situation or thematics

*Steps*
- similar as before

##### Case 5 - Rehearse learned material

*Steps*
- Go to rehearse page
- Define rehearse material range from filter options:
  - spaced-repetition - auto - no filters selected
  - root item - a specific item or item type containing target items you want to rehearse - could be a lesson, conversation, specific song lyrics and so on
  - target item type - phrase, word, character
- Choose rehearse mode - reading, listening, speaking, writing
- Choose flashcard list count limit for single session
- Rehearse

## Materials Structure

- Official Courses
  - Lessons - **Done**
    - Conversations
    - Phrases
    - Situations
    - Articles
    - Vocabulary
    - Grammar points
- Writing Scripts
  - Kana
    - Hiragana 
    - Katakana
  - Kanji
    - Kanji Characters
    - Radicals
  - Words
- JLPT Categorization
  - N5, N4, N3..
- Vocabulary
  - By Linguistics
    - Particles, Pronouns, Adjectives, Verbs, Auxilary Verbs, Nouns, Names, Conjunctions, Numerals, Prepositions, Classifiers, Adverbs, Interjections
      - Word Dictionary Writings
        - Word Meanings
        - Word Inflections
        - Word Compounds
  - By Theme
    - Colors, Numbers, Animals, Names and tons more..
  - Similar words
    - By similar but not exactly the same writing, sound, ex. 怖い - 可愛い
    - By the same kana writing, sound, but different pitches, ex. 洗車 - 戦車 or 橋 - 箸
    - By same writing, sounds, almost the same meanings but just little bit niuanced, ex. 思い -　想い
- Writing type preference
  - Usually kana (hiragana) alone
  - Usually kanji (and kana of course, depending on a word)
  - Katakana (not just foreign words, but also normal words but to make them look different, or titles)
  - Character
    - Hiragana Character
    - Katakana Character
    - Kanji Character
    - Radical Character
- Phrases
  - Popular phrases
  - Situations
  - Level of difficulty (simple/beginner, advanced..)
  - Levels of politeness (rude, informal, formal, humble/honorific)
- Media
  - Anime, manga, songs, articles, arts, stories, TV dramas
- Genders - vocabulary or phrases used depending on a person's gender
- Age jargons - vocabulary or phrases used depending on a person's age
- Keigo - rude, informal, formal, humble/honorific
- Dialects - Kanto, Kansai, Kyushuu and more..

## View Design

### Word View - Verb

- Value/Original/Word - なる
- Meanings
  - なる - to become
  - なる - to get
  - ...
- Inflections
  - Present
    - Positive - なる
    - Negative - ならない
  - Present, Formal
    - Positive - なります
    - Negative - なりません
  - Past
    - Positive: なった
    - Negative: ならなかった
  - Past, Formal
    - Positive: なりました
    - Negative: なりませんでした
  - Progressive
    - Positive: なっている
    - Negative: なっていない
  - Potential
    - Positive: なれる
    - Negative: なれない
  - Passive
    - Positive: なられる
    - Negative: なられない
  - Causative
    - Positive: ならせる
    - Negative: ならせない
  - Causative Passive
    - Positive: ならせられる
    - Negative: ならせられない
  - Imperative
    - Positive: なれ
    - Negative: なるな
  - Violitional
    - Positive: なろう
  - Violitional, Formal
    - Positive: なりましょう
  - Conditional (reba)
    - Positive: なれば
    - Negative: ならなければ
  - Conditional (tara)
    - Positive: なったら
    - Negative: ならなかったら
- Particles (rather for verbs)
- Compounds (rather for verbs?)

### Word View - Adjective, い

- Value/Original/Word - 重い
- Meanings
  - 重い - heavy (by weight)
  - 重い - slow (by weight)
  - 重い - serious (heavy)
- Inflections
  - Present
    - Positive - 重い
    - Negative - 重くない
  - Past
    - Positive - 重かった
    - Negative - 重くなかった

### Word View - Noun and others

- Value/Original/Word - 日
- Meanings
  - 日 - ひ, sun
  - 日 - にち, day
  - 日 - か, day, suffix













