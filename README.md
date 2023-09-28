# Kitsune Study Documentation

&nbsp;&nbsp;&nbsp;&nbsp; Japanese language learning app where you can add and explore various materials and turn anything into flashcards.

## General

#### Technologies
&nbsp;&nbsp;&nbsp;&nbsp; NET Core, C#, HTML, CSS, Blazor Server, MongoDb, AWS, Docker

#### Demo Urls
&nbsp;&nbsp;&nbsp;&nbsp; https://kitsunestudy.net

#### Features

*Current State*

- Official Content Creation - Course, Conversations and phrases - add, edit and explore learning content - text and audio
- Flashcard List Generation - Phrases - turn any lesson or conversation phrases into a flashcard list in any mode - reading, listening, speaking
- Progress Tracking progress - view basic statistics of learned material
- Spaced Repetition Flashcard Generation - Rehearse learned phrases in spaced repetition mode with basic filtering options by mode

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
![System Context Diagram](/docs/images/System%20Context%20Diagram.png)

## Environment

### Development
- IDEs - Visual Studio 2022, Notepad++
- Task Management - Trello
- Source Version Control - Git, Github, SourceTree
- MongoDb Database Tools - MongoDb Cloud, MongoDb Shell, Studio 3T, MongoDb Compass
- Design Tools - Miro

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
    - Verbs, Nouns, Adjectives, Particles
      - Word Dictionary Writings
        - Word Meanings
        - Word Inflections
        - Word Compounds
        - 
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
