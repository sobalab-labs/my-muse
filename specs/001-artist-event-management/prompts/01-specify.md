# Prompt Record: Specify Phase

**Timestamp**: 2026-10-10
**Phase**: Specify (`/speckit.specify`)

## User Prompt

```text
project: web app, domain: musician(we will call "artist") and concert(we weill call "event"), what user requirements to meet: 1. manage(CRUD) list of favorite artists. 2. view data of future events of all favorite artists. 3. use conventional(industry standard style) filter/sort e.g. only see certain artist's event, only see certain region(e.g. japan / korea) events, sort by d-day, ... 4. be authenticated using standard third party oauth (e.g. google, kakao) 5. register(request) for "send email when at new event open" or "send kakao message at new event open" to backend. (the message itself is not this project's responsibility. we are only responsible for configuring/requesting this feature subscription). **following is context only to help you(agent) and do not explicitly include these to result or documents: rough domain data example -artist(name, genre, nation, list of website/sns urls) -concert(artist(s), date, place, duration, ticekting url, official announcement url), is a service with internalization that supports jp and kr.
```

## Agent Action

- Generated short name: `artist-event-management`
- Created feature directory: `specs/001-artist-event-management`
- Created feature spec file: `specs/001-artist-event-management/spec.md`
- Created prompt record: `specs/001-artist-event-management/prompts/01-specify.md`
