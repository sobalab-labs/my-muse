# Data Model: Artist & Concert Event Management Hub

**Branch**: `001-artist-event-management` | **Date**: 2026-10-21 | **Spec**: [spec.md](./spec.md)

## 1. Core Entities & Attributes

### User
Represents an authenticated application user.
- `id`: string (UUID)
- `email`: string
- `name`: string
- `oauthProvider`: `'google' | 'kakao'`
- `oauthId`: string
- `preferredLocale`: `'ja' | 'ko'`
- `onboardingCompleted`: boolean
- `createdAt`: ISO 8601 timestamp
- `updatedAt`: ISO 8601 timestamp

### Artist
Represents a musician or band (Master data managed exclusively by backend).
- `id`: string (UUID)
- `name`: string
- `genre`: string
- `nation`: string (e.g., `'Japan'`, `'Korea'`, `'Global'`)
- `snsUrls`: Array<{ platform: string; url: string }>
- `imageUrl`: string (optional)
- `createdAt`: ISO 8601 timestamp

### Event (Concert)
Represents a musical performance or concert event.
- `id`: string (UUID)
- `artists`: Array<Artist>
- `title`: string
- `date`: ISO 8601 datetime
- `place`: string (Venue name & location)
- `duration`: string (e.g., `"120 min"`)
- `ticketingUrl`: string
- `officialAnnouncementUrl`: string
- `region`: `'japan' | 'korea' | 'other'`
- `createdAt`: ISO 8601 timestamp

### NotificationSubscription
Represents user configuration for artist-specific and general weekly notification subscriptions.
- `id`: string (UUID)
- `userId`: string (FK -> User.id)
- `channels`: { email: boolean; kakao: boolean }
- `artistSubscriptions`: Array<{
    artistId: string;
    newEvents: boolean;
    ticketOpenBundle: boolean; // Controls day before, 30 min before, at open
    discography: boolean; // Priority 1 > 2 > 3 (newEvents > ticketOpenBundle > discography)
  }>
- `generalWeeklyUpdates`: boolean
- `updatedAt`: ISO 8601 timestamp

## 2. Relationships

- **User** 1:N **FollowedArtist** (Many-to-many relationship between User and Artist)
- **User** 1:1 **NotificationSubscription**
- **Artist** 1:N **Event** (An artist can be associated with many events; an event can feature multiple artists)

## 3. State Transitions & Validation Rules

- **Follow / Unfollow**: Authenticated users can add/remove artists from their followed list. Unauthenticated users trigger an OAuth sign-in prompt.
- **Onboarding State**: `onboardingCompleted` transitions from `false` to `true` upon completing or skipping the initial artist selection step.
- **Notification Priority**: In backend processing and UI display, artist notification types follow priority: 1. New Event > 2. Ticket Open Bundle > 3. Discography.
- **Account Deletion**: Permanent account deletion removes the User record, followed artists associations, and notification subscriptions.
