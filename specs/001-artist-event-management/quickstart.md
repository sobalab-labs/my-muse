# Quickstart Validation Guide: Artist & Concert Event Management Hub

**Branch**: `001-artist-event-management` | **Date**: 2026-10-21

## 1. Prerequisites

- Node.js 20+ installed
- Frontend dependencies installed (`npm install`)
- Backend API service running and configured

## 2. Setup & Run Commands

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Run linting
npm run lint

# Build production bundle
npm run build
```

## 3. End-to-End Validation Scenarios

### Scenario 1: Authentication & Localization
1. Navigate to `http://localhost:3000/ja/login`.
2. Click "Sign in with Google" or "Sign in with Kakao".
3. Verify successful redirect to onboarding (`/ja/onboarding`) for new users or Home (`/ja/home`) for returning users.
4. Go to Settings (`/ja/settings`) and switch language to Korean (`ko`); verify UI labels update instantly.

### Scenario 2: Artist Discovery & Following
1. Navigate to Search (`/ja/search`).
2. Type an artist name keyword; verify matching results appear.
3. Select an artist to view Artist Detail (`/ja/artists/[id]`).
4. Click Follow; verify authentication prompt appears for unauthenticated users, or successful follow state for authenticated users.
5. Check "My Artists" hub (`/ja/artists/followed`) to manage followed artists.

### Scenario 3: Event Feed, Filters, Sorting & Details
1. Navigate to Home (`/ja/home`); verify Trending scope for unauthenticated users or Following scope for authenticated users.
2. Apply region filter (e.g., Japan) and sort by d-day; verify instant list update (<200ms).
3. Switch between List and Month Calendar views.
4. Click an event to view Event Detail (`/ja/events/[id]`); test Add to Calendar (ICS), Share, and Open Ticketing URL actions.

### Scenario 4: Notification Subscriptions
1. Navigate to Settings (`/ja/settings`).
2. Configure artist-specific notification subscriptions (new event, ticket open bundle, discography) with 1 > 2 > 3 priority and general weekly event updates.
3. Verify successful update confirmation toast (`SN-04`).
